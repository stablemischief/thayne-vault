---
title: PR #581 (Issue #569) — Testing in Progress
pr: https://github.com/mdornich/agrellus-crop-finance-mvp/pull/581
issue: 569
branch: archon/task-fix-issue-569
base: staging
status: Testing Step 1
test_app_farm_plan: 2deb511c-798b-459d-8571-b20870c7649f
test_app_legacy_fsa: 7b41db45-1067-41f9-b41e-f4bfd1ae1df0
analyst_id: c19dff92-ac25-4896-a698-d8a3cbaa8eaa
created: 2026-04-23
---

# PR #581 Test Plan — #569 FSA-578 Fallback Removal

## Context

PR #581 removes 9 FSA-578 fallback read sites across 4 services so that `CropPlanEntry` (materialized by `FarmPlanSyncService` from PR #579) is the single source of truth for current-year farm plan data. #577/PR #579 shipped yesterday and is merged.

## What's on the branch

Two commits on `archon/task-fix-issue-569`:

1. `37443fc` — core 9-kill-site removal (Archon's initial implementation)
2. `67937f5` — self-fix delta (20 new tests, deterministic slot tiebreaker, `_resolve_yield` NI fix, new `test_gap_field_prepopulate_income.py`)

The second commit was **uncommitted in the Archon worktree** after the workflow finished — Archon respected the CLAUDE.md "do not commit/push" rule for the review/self-fix phase. Caught during teardown when GitHub Desktop failed to check out the branch (worktree was holding it). Committed + pushed on 2026-04-23 after James approved.

## Files changed (merged PR state)

- `api-backend/app/services/gap_field_service.py` — kill site #1, #2, #3 (removed `_build_fsa_farm_details` helper)
- `api-backend/app/services/projected_revenue_service.py` — kill sites #4, #5, #6 + deterministic tiebreaker
- `api-backend/app/services/calculation_engine.py` — kill site #8 + mirrored tiebreaker
- `api-backend/app/services/gap_analysis_service.py` — kill site #9 (`FSA_crop_year` → `application.crop_year`)
- `api-backend/tests/unit/test_projected_revenue.py` — +296 lines / 20 new tests
- `api-backend/tests/unit/test_pipeline_integration.py` — +78 lines / crop_year resolution tests
- `api-backend/tests/unit/test_gap_field_prepopulate_income.py` — NEW file / 3 tests
- `api-backend/tests/unit/test_calculation_engine_lands.py` — removed obsolete TestMaterializeLandTracts
- `api-backend/tests/unit/test_calculation_engine_expenses.py` — swapped FSA_ → LM_ fixtures

## CI state as of testing start

- Build Frontend / Lint Backend / Test Backend: all passing on commit `37443fc`
- After pushing `67937f5`: all re-running (pending at start of test session)

## Key gotchas learned this session

1. **`module_fields` is not a table.** `LM_crop_*` / `PI_crop_*` / etc. are **computed on-demand** by `module_service._get_module_fields` — never persisted. SQL verification of module-field output is impossible; must use UI + backend logs instead.
2. **`extraction_results` has no `application_id`.** It's linked to applications via `document_id → documents.application_id`. Join through `documents` table if needed to filter by application.
3. **`applications` column is `application_status`, not `status`.** Same gotcha as yesterday's #579 test plan.
4. **`applications` PK is `application_id`, not `id`.** Same for all tables in this schema (`<table>_id` convention).

## Test apps pre-identified

- **Farm Plan app (for happy-path tests):** `2deb511c-798b-459d-8571-b20870c7649f` — from yesterday's #579 testing. Has 2 FARM_PLAN land_tracts (Corn/1234/NI/325 + Sunflowers, added in Step 8). Also has crop_plan_entries for both.
- **Legacy FSA app (for empty-fallback tests):** `7b41db45-1067-41f9-b41e-f4bfd1ae1df0` — one of 10 LEGACY_FSA apps identified during #577 Step 7. Has 8 legacy tract_crops, zero crop_plan_entries.

## Full 5-Step Test Plan

### Step 1 — Farm Plan app populates Projected Income from CropPlanEntry ✅ PASSED (2026-04-23)

**Action:**
1. Open app `2deb511c...` → Projected Income module
2. Observe UI

**Upstream sanity SQL:**
```sql
SELECT farm_number, crop, practice, dry_acres, irrigated_acres
FROM crop_plan_entries
WHERE application_id = '2deb511c-798b-459d-8571-b20870c7649f'
ORDER BY row_order;
```
Expected: 2 rows — Corn/1234 + Sunflowers.

**UI expected:**
- Projected Income shows Corn + Sunflowers
- Acres match Farm Plan (325 Corn + whatever Sunflowers)
- NO phantom Cotton/Wheat/Barley rows

**Backend log expected:** `_prepopulate_projected_income` invocation, NO `fsa_extraction` / `_build_fsa_farm_details` / `FSA_crop_` entries.

**Earlier bad SQL attempts (don't repeat):**
- `SELECT ... FROM module_fields` — no such table
- `SELECT ... FROM extraction_results WHERE application_id = ...` — no such column

### Step 2 — Legacy FSA-only app shows EMPTY projected income (critical!)

**Action:**
- Open legacy app `7b41db45-1067-41f9-b41e-f4bfd1ae1df0` → Projected Income

**Upstream sanity SQL:**
```sql
SELECT COUNT(*) AS farm_plan_entries
FROM crop_plan_entries
WHERE application_id = '7b41db45-1067-41f9-b41e-f4bfd1ae1df0';
```
Expected: `0` (legacy app has no Farm Plan data).

**UI expected:** Projected Income shows **empty state** — no rows, no pre-populated values. The legacy tract_crops (which tie to LEGACY_FSA source) should NOT surface here.

**Why this is the most important step:** #569's core promise is "no silent FSA fallback on current-year calculations." If this legacy app shows crop rows in Projected Income, the fix failed.

**Backend log expected:** May see a WARN log like `empty_crop_plan` / `no_farm_plan_data` — that's by design (added in self-fix delta).

### Step 3 — Crop year resolution uses Application, not FSA

**Action:**
- Check crop_year state for both test apps:
```sql
SELECT application_id, crop_year
FROM applications
WHERE application_id IN (
  '2deb511c-798b-459d-8571-b20870c7649f',
  '7b41db45-1067-41f9-b41e-f4bfd1ae1df0'
);
```

**Expected behavior:**
- If `crop_year` is set on an app → downstream calculations use that year
- If `crop_year` is null → uses `datetime.now().year` (= 2026), NOT any FSA_crop_year value
- Log should show zero `FSA_crop_year` reads

**Optional:** Temporarily null-out the crop_year on one test app, trigger a projected revenue calculation, confirm it uses 2026. Restore the value after.

### Step 4 — Projected Revenue uses Farm Plan acres

**Action:**
- On app `2deb511c...`, trigger projected revenue calculation (likely via Projected Income module save/refresh)
- Watch backend logs for `calculate_projected_revenue` events

**UI expected:** Revenue values ≈ (acres × yield × price) using Farm Plan acres. Numbers should change if you edit Farm Plan and re-save.

**Backend log expected:**
- `calculate_projected_revenue` fires
- `_assign_crop_slots` fires with deterministic tiebreaker (name-ascending secondary sort — part of self-fix delta)
- No reads of `FSA_crop_N_acres`

### Step 5 — Regression: #577 flows still work

**Action:**
- On app `2deb511c...` → Farm Plan
- Add a new row (Wheat/5555/NI/100)
- Trash a row
- Back → Next

**Expected:**
- Per-row operations from #579 still work
- No new error toasts (the api-client 204 fix from yesterday still holds)
- land_tracts / tract_crops still sync via FarmPlanSyncService

**Verify:**
```sql
SELECT source, COUNT(*) FROM land_tracts
WHERE application_id = '2deb511c-798b-459d-8571-b20870c7649f'
GROUP BY source;
```
Should match visible Farm Plan row count.

## Current state at save time

- **Step 1 ✅ PASSED** — crop_plan_entries SQL confirmed 2 rows; UI showed exactly Corn/1234/325 + Sunflowers/2341/50 with no phantom crops. Yields were 0 (yield_history not populated for this test app — orthogonal to #569).
- **Step 2:** NEXT — legacy FSA-only app empty-state verification. This is the most load-bearing step in the plan.
- Steps 3–5 not started.

## If session crashes, to resume

1. Read this file + `~/Developer/Apps/Thayne-Brain/thayne-vault/Projects/Agrellus/pr-579-test-plan.md` for #577 context
2. Read `feedback_archon_worktree_teardown.md` in auto-memory for the worktree-teardown rule
3. Check which step James last reported on (may need to ask him)
4. Verify PR #581 hasn't been merged yet: `cd ~/Developer/GitHub/agrellus-crop-finance-mvp && gh pr view 581 --json state,mergedAt`
5. Verify current working directory or branch checkout state with `git status` + `git log --oneline -5`
6. Continue from the next unverified step

## Follow-up issues Archon suggested (unreviewed)

5 follow-up issues were suggested by Archon during the workflow. Not yet reviewed or filed. Pull via:
```bash
cd ~/Developer/GitHub/agrellus-crop-finance-mvp && gh issue view 569 --comments
```
Look at the latest comment from stablemischief (2026-04-22/23) for the list.
