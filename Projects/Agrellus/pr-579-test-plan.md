---
title: PR #579 (Issue #577) — 8-Step Manual Test Plan
pr: https://github.com/mdornich/agrellus-crop-finance-mvp/pull/579
issue: 577
branch: archon/task-fix-issue-577-remove-fsa-from-lands-farmed
base: staging
application_id: de44d6f8-5ccf-4568-9ba3-aacc3bb79989
analyst_id: c19dff92-ac25-4896-a698-d8a3cbaa8eaa
migration: 7d8e9f0a1b2c (from 5a6b7c8d9e0f)
status: Step 4 failing (delete sync not firing)
created: 2026-04-22
---

# PR #579 Test Plan — Reconstructed From Session bc75a7a8

Reconstructed after claude.ai image-size errors killed the original session mid-Step-4. See prior session transcript at `~/.claude/projects/-Users-jw-dev-Developer-Apps-Thayne-Brain/bc75a7a8-9347-4048-a06b-8cc189aea3f4.jsonl` if needed.

## Context

PR #579 implements Issue #577 (remove FSA from Lands Farmed, add `land_tracts.source` column: FARM_PLAN / LEASE_AGREEMENT / LEGACY_FSA). Depends on PR #554 per-row save infrastructure. Plan tests the `FarmPlanSyncService` idempotent rebuild behavior end-to-end through the wizard UI against the real backend + Supabase cloud DB.

**Repo:** `~/Developer/GitHub/agrellus-crop-finance-mvp`
**Backend:** `~/Developer/GitHub/agrellus-crop-finance-mvp/api-backend`
**Service under test:** `app.services.farm_plan_sync_service.sync_from_crop_plan`
**Tables:** `land_tracts` (new `source` col), `tract_crops`, `application_farms`, `applications`

## Pre-flight migration — DONE

Supabase pooler closes mid-migration, so Alembic upgrade was applied via GUI SQL Editor:

```sql
ALTER TABLE land_tracts ADD COLUMN IF NOT EXISTS source VARCHAR(50);
UPDATE land_tracts SET source = 'LEGACY_FSA' WHERE source IS NULL;
ALTER TABLE land_tracts ALTER COLUMN source SET NOT NULL;
CREATE INDEX IF NOT EXISTS ix_land_tracts_source ON land_tracts (source);
UPDATE alembic_version SET version_num = '7d8e9f0a1b2c' WHERE version_num = '5a6b7c8d9e0f';
```

Verified: 14 pre-existing rows backfilled as LEGACY_FSA. `alembic_version = 7d8e9f0a1b2c`.

## Step-by-step progress

### Step 1 — Create app, land on Farm Plan ✅ PASSED

New app → Steps 1/2/3 wizard. application_id = `de44d6f8-5ccf-4568-9ba3-aacc3bb79989`. First crop row created live, `source='FARM_PLAN'` confirmed.

### Step 2 — Add two more Farm Plan rows ✅ PASSED

| # | County | Farm # | Crop | Practice | Acres |
|---|---|---|---|---|---|
| 1 | Lubbock | 4101 | Cotton | IRR | 350 |
| 2 | Lubbock | 4101 | Wheat | NI | 230 |
| 3 | Lubbock | 4102 | Cotton | IRR | 300 |

Counts: `farm_plan_tracts=2`, `tract_crops=3`, `app_farms=2`. Correct dedup on farm_number.

### Step 3 — Click Next (bulk PUT + idempotent rebuild) ✅ PASSED

Counts stable at 2/0/3/2 after rebuild. No LEGACY_FSA contamination. Bulk PUT + FarmPlanSyncService cooperating.

### Step 4 — Trash Wheat/NI row ⚠️ FAILING — **CURRENT BLOCKER**

**Expected counts after trash:** `farm_plan_tracts=2`, `tract_crops=2`, `app_farms=2`. Farm 4101 persists because Cotton/IRR still references it.

**Reported:** "doesn't look like it sync'd after delete."

Backend log snippet James pasted showed only the two pre-delete create events (tracts_created=1→2, crops_created=2→3 at ~20:23:55 and ~20:24:28). **No `farm_plan_sync_complete` event after the DELETE.** Log cut off mid-record at `20:24:28`.

**Hypotheses:**
- **(a)** DELETE endpoint not invoking `FarmPlanSyncService.sync_from_crop_plan()` — real #577 bug
- **(b)** Trash icon bound to local state only, no API call fired — frontend bug in #554 infrastructure

**Diagnostic next:** check browser DevTools Network tab for the `DELETE /api/v1/applications/{id}/crop-plan/entries/{entry_id}` request. If absent → (b). If present (201/204) but no sync event → (a).

### Steps 5–8 — NOT YET AUTHORED

Walked one step at a time; pending resolution of Step 4. PR #579 body likely has the full plan; should cover Lease Agreement extraction sync, FSA-578 skip, legacy preservation, and final submit reconciliation.

## Verify queries (reusable)

```sql
-- Summary counts
SELECT
  (SELECT COUNT(*) FROM land_tracts WHERE application_id = 'de44d6f8-5ccf-4568-9ba3-aacc3bb79989' AND source = 'FARM_PLAN') AS farm_plan_tracts,
  (SELECT COUNT(*) FROM land_tracts WHERE application_id = 'de44d6f8-5ccf-4568-9ba3-aacc3bb79989' AND source = 'LEGACY_FSA') AS legacy_fsa_tracts,
  (SELECT COUNT(*) FROM tract_crops tc JOIN land_tracts lt ON tc.tract_id = lt.tract_id WHERE lt.application_id = 'de44d6f8-5ccf-4568-9ba3-aacc3bb79989') AS tract_crops,
  (SELECT COUNT(*) FROM application_farms WHERE application_id = 'de44d6f8-5ccf-4568-9ba3-aacc3bb79989') AS app_farms;

-- Detail
SELECT lt.farm_number, lt.county, lt.source, tc.crop_type, tc.practice, tc.acres
FROM land_tracts lt
JOIN tract_crops tc ON tc.tract_id = lt.tract_id
WHERE lt.application_id = 'de44d6f8-5ccf-4568-9ba3-aacc3bb79989'
ORDER BY lt.farm_number, tc.crop_type;
```

## Bugs/notes from this session

- Archon `--branch` flag ignored, created new worktree off staging — resolved via `archon-resolve-conflicts` cherry-pick
- Branch-protection hook regex false-positive on `-f` substring — fixed in `~/.claude/hooks/hookmaster/branch-protection/script.sh`
- PK convention: `<table>_id`, not `id`
- Squash-merge required (PR title overrides the `chore: Auto-commit workflow artifacts` commit message)
