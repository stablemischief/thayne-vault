---
title: PR #586 (Issue #580) — Testing in Progress
pr: https://github.com/mdornich/agrellus-crop-finance-mvp/pull/586
issue: 580
branch: archon/task-fix-issue-580
base: staging
status: IN PROGRESS — Step 1
test_app_id: 70108db3-6007-4bb6-bc4e-84bb327626be
created: 2026-04-24
---

## Schema quick-reference (baked in for this session)

**`crop_plan_entries`** — source of truth, primary table for most SQL
- PK: `id` (UUID)
- FK: `application_id`
- Fields: `county`, `farm_number`, `crop`, `practice`, `dry_acres`, `irrigated_acres`, `row_order`
- Unique constraint: `(application_id, county, farm_number, crop, practice)`
- Practice codes: **`IRR`** (irrigated) or **`NI`** (non-irrigated)
- Yield projection fields: `dry_yield_avg`, `irr_yield_avg`, `insurance_level_dry`, `insurance_level_irr`, `commodity_price`, `total_acres`, `total_yield`, `avg_yield`

**`land_tracts`** — projection, rebuilt by `FarmPlanSyncService`
- PK: `tract_id` (NOT `id`)
- FK: `application_id`, `farm_id`, `document_id`
- Fields: `farm_number`, `tract_number`, `county`, `total_cropland_acres`, `dryland_acres`, `irrigated_acres`, `fallow_acres`, `source`, `data_source`
- `source` routing key: `FARM_PLAN` / `LEASE_AGREEMENT` / `LEGACY_FSA` — FarmPlanSync only touches `FARM_PLAN` rows

**`tract_crops`** — crop-level projection under each tract
- PK: `tract_crop_id`
- FK: `tract_id` → `land_tracts.tract_id`
- Fields: `crop_type` (NOT `crop`), `acres`, `practice`

# PR #586 Test Plan — Crop Plan Per-Row Auto-Save (#580)

## Context

PR #586 wires per-row `PATCH /crop-plan/entries/{id}` on field blur with a 400ms keystroke debounce, adds a header-level save-status indicator, and flushes in-flight PATCHes on `beforeunload` via `fetch({ keepalive: true })`. Bulk `PUT /crop-plan` on Next remains as the safety net. Supersedes #576 (manual Save button idea).

## What's on the branch (3 commits)

1. `a156900` — Archon initial implementation (forwardRef + flushAll, debounce, blur flush, keepalive, abort-on-stale, FarmPlanSync hook, route normalize)
2. `331e76c` — Self-fix polish (C1/H1/H2/M1–M8/L1 + 7 new tests)
3. `dfac0ed` — Ruff format fix on the integration test signature

## Files changed (18 total)

**Backend:**
- `api-backend/app/api/v1/endpoints/crop_plans.py` — PATCH route normalized to `/crop-plan/entries/{entry_id}`
- `api-backend/app/services/crop_plan_service.py` — `update_entry` now invokes `FarmPlanSyncService.sync_from_crop_plan`; log-and-swallow on sync failure
- `api-backend/tests/integration/test_crop_plan_service_practice.py` — new sync + sync-failure coverage

**Frontend:**
- `web-frontend/src/lib/api-client.ts` — added `cropPlans.updateEntry` with AbortSignal
- `web-frontend/src/components/intake/CropPlanForm.tsx` — forwardRef + `flushAll()`, debounce, blur/pagehide flush, abort + no-stomp, per-row error UI
- `web-frontend/src/components/intake/IntakeWizard.tsx` — awaits `flushAll()` before bulk PUT on Next
- `web-frontend/src/app/applications/new/page.tsx` — wires `updateCropPlanEntry` + `getAuthToken`
- `web-frontend/src/app/applications/[id]/data-collection/page.tsx` — same wiring for edit-mode
- `web-frontend/src/components/gap-analysis/LandsGrid.tsx` — C1 URL fix (path normalization)
- Tests: `CropPlanForm.test.tsx` (+338), `IntakeWizard.test.tsx` (+86)

## CI state as of testing start

- Test Backend: ✅ pass
- Build Frontend: ✅ pass
- Lint Backend: ✅ pass (after `dfac0ed` fix)
- CodeRabbit: ✅ reviewed

## Known deferred / out of scope

- **Retry CTA on failure (L4)** — blocked by stale-closure bug in `recomputeStatus`. Tracked in follow-up issue #587. **Do not test this UX — it won't surface.**
- **sessionStorage unload restore (M2 Option C)** — not implemented. Tracked in #587.
- **8 pre-existing frontend test failures** — unrelated to #586. Tracked in #588.

## Test environment setup

1. Pull latest on `archon/task-fix-issue-580`:
   ```bash
   cd /Users/jw-dev/Developer/GitHub/agrellus-crop-finance-mvp
   git fetch origin && git checkout archon/task-fix-issue-580 && git pull
   ```
2. Backend running locally (`api-backend` on port matching frontend `.env.local`)
3. Frontend running locally (`bun run dev` in `web-frontend`)
4. Browser with DevTools **Network tab pinned open** — we'll inspect PATCH calls in most steps
5. Create a fresh test application via the intake wizard (used for Section A) OR pick a known existing app with a populated Farm Plan (used for Section B)

## Test scenarios

### Section A — Intake Wizard (applications/new)

---

### Step 1 — Baseline row add persistence (sanity check)

**Goal:** Confirm existing per-row CREATE (#554) still works — baseline for the rest.

**Steps:**
1. Start a new intake → advance to Step 3 (Farm Plan)
2. Click "Add Crop Row"
3. Fill in: Crop=Corn, Farm#=1234, Practice=IR, Acres=300
4. Tab out of the row (focus leaves all fields)
5. DevTools → Network: observe `POST /crop-plan/entries` fires

**Expected:**
- Network shows a single POST with 201 response
- Row gets an `id` field populated in React state (you can't see this directly, but next step confirms it)
- Save-status indicator briefly shows `Saving…` → `Saved ✓` (fades)

**Pass criteria:** POST returns 201, row persists after refresh.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 2 — Per-row UPDATE on blur (primary scenario)

**Goal:** The core promise of #580 — edit an existing row, blur, edit persists.

**Steps:**
1. In the row from Step 1, change Acres: 300 → 325
2. Click somewhere outside the row (blur)
3. DevTools → Network: observe `PATCH /crop-plan/entries/{id}` fires within 400ms
4. Wait for PATCH to return
5. **Refresh the browser (Cmd+R)**
6. Advance back to Step 3

**Expected:**
- PATCH fires with body containing updated acres
- PATCH returns 200
- Save-status shows `Saving…` → `Saved ✓`
- After refresh, row shows Acres=325 (not 300)

**Pass criteria:** PATCH fires, returns 200, value persists after refresh.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 3 — Debounce consolidation (rapid edits → single PATCH)

**Goal:** Rapid keystrokes shouldn't fire a PATCH per keystroke.

**Steps:**
1. Focus the Acres field of an existing row
2. Type `1`, `2`, `3`, `4`, `5` rapidly (within ~2 seconds)
3. DevTools → Network: observe PATCH frequency
4. Stop typing, wait 500ms

**Expected:**
- **Zero PATCHes while actively typing** (debounce timer keeps resetting)
- **One PATCH fires ~400ms after the last keystroke**
- Body contains the final value (12345 or whatever you ended at)

**Pass criteria:** Single PATCH with final value, not one-per-keystroke.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 4 — Blur flushes pending debounce immediately

**Goal:** If user tabs out before the 400ms debounce fires, the PATCH should flush immediately.

**Steps:**
1. Focus Acres field
2. Type a new value (e.g. `999`)
3. **Immediately tab out / click outside the row** (within ~100ms of last keystroke)
4. DevTools → Network: observe PATCH timing

**Expected:**
- PATCH fires within ~50ms of blur (not waiting for the full 400ms debounce)
- Body contains `999`

**Pass criteria:** Blur cancels debounce and flushes immediately.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 5 — Tab-close keepalive flush (critical data-loss test)

**Goal:** Even if user closes the tab before debounce fires, the edit should persist.

**Steps:**
1. Focus Acres field
2. Type a distinctive new value (e.g. `777`)
3. **Immediately close the tab** (Cmd+W within ~100ms)
4. DevTools → Network: in the prior tab's closed state, you may need to re-open DevTools before close to see the keepalive fetch
5. Re-open the app, navigate back to Step 3 Farm Plan

**Expected:**
- The edit persists — row shows `777` on reload
- Console/Network shows a PATCH was sent via `fetch({ keepalive: true })` during pagehide/beforeunload

**Pass criteria:** Value persists after tab close.

**Gotcha:** Closing a tab too fast can be hard to trigger repeatably. If persistence fails, try with slight delay (200–300ms) to confirm the blur-flush path works; then try again faster.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 6 — Save-status indicator states

**Goal:** Verify the four indicator states render correctly.

**Steps:**
1. Observe indicator at idle (no dirty rows) — should be hidden OR show `Saved ✓`
2. Edit a field, watch indicator during the debounce window → `Saving…` + spinner
3. After PATCH returns → `Saved ✓` (auto-fades after ~2s)
4. (Error state tested in Step 10)

**Expected:**
- Indicator renders inline with the "Farm Plan" step header
- Uses muted text for Saving/Saved; destructive color for error
- No layout shift when state transitions

**Pass criteria:** All three non-error states render correctly with expected styling.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 7 — Next click awaits in-flight PATCH

**Goal:** Clicking Next during an in-flight PATCH should await it before bulk PUT fires.

**Steps:**
1. Edit a field to a new value (e.g. `555`)
2. **Quickly click Next button** while the debounce is still pending or PATCH is mid-flight
3. DevTools → Network: observe order of requests

**Expected:**
- PATCH fires and completes first (or is flushed synchronously)
- **Then** bulk `PUT /crop-plan` fires with the updated value already reflected
- Wizard advances to Step 4

**Pass criteria:** PATCH completes before bulk PUT; no race, no lost edit.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 8 — Bulk PUT safety net on Next still fires

**Goal:** Confirm the existing bulk-save-on-Next contract from #554 is preserved.

**Steps:**
1. Make one edit (triggers a PATCH)
2. Wait for PATCH to complete (`Saved ✓`)
3. Click Next
4. DevTools → Network: confirm `PUT /crop-plan` still fires (even though all rows are already synced)

**Expected:**
- PUT fires unconditionally on Next — this is intentional belt-and-suspenders
- PUT body matches current row state

**Pass criteria:** PUT fires, returns 200, wizard advances.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 9 — FarmPlanSync rebuild after PATCH

**Goal:** Verify `FarmPlanSyncService.sync_from_crop_plan` runs after PATCH and `tract_crops` reflects the change.

**Steps:**
1. Note current `tract_crops` state for this application:
   ```sql
   SELECT crop, practice, acres, source
   FROM tract_crops tc
   JOIN land_tracts lt ON tc.tract_id = lt.id
   WHERE lt.application_id = '<your-test-app-id>'
   ORDER BY lt.farm_number, tc.crop;
   ```
2. Change Practice on one row: IR → DR
3. Blur; wait for `Saved ✓`
4. Re-run the SQL above

**Expected:**
- Row's `practice` column changes to DR in `tract_crops`
- No orphaned rows
- Backend logs show `sync_from_crop_plan` invocation

**Pass criteria:** Projection matches new crop-plan state after PATCH (no stale data).

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 10 — Error recovery: offline edit

**Goal:** Network failure should show error state + retain local typing (no revert).

**Steps:**
1. DevTools → Network → set **Offline**
2. Edit a field (e.g. Acres → `888`)
3. Blur
4. Observe UI state:
   - Save-status indicator → `Save failed — retry` (destructive color)
   - Row shows inline red dot or error marker
   - Acres field still shows `888` (not reverted)
   - Destructive toast shows backend/network error detail
5. DevTools → Network → set **Online**
6. Click back into the field and blur again (or re-edit)

**Expected (offline):**
- Local typing retained (`888` visible)
- Error status shown
- Destructive toast fired

**Expected (back online + re-blur):**
- New PATCH fires, returns 200
- Error indicator clears
- Status → `Saved ✓`

**Known limitation:** Clicking the header `Save failed — retry` itself won't retry — that's the L4 stale-closure bug tracked in #587. Recovery works via re-blur / re-edit / Next click.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 11 — Duplicate row does not block PATCH, does block Next

**Goal:** PATCH persists regardless; Next still validates.

**Steps:**
1. Add two rows with identical Crop=Corn, Farm#=1234, Practice=IR
2. Observe inline duplicate marker appears (from #572/#573)
3. Edit Acres on one of the duplicate rows
4. Blur — observe PATCH fires and returns 200 (persisted despite duplicate)
5. Click Next

**Expected:**
- PATCH fires on edit regardless of duplicate state
- Next click is blocked with duplicate-detection error (existing #573 behavior)
- Inline error list shows under Step 3

**Pass criteria:** PATCH doesn't skip on duplicates; Next still guards.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 12 — New row edit before POST completes (queued edit path)

**Goal:** If user edits a brand-new row before its POST returns, edits should queue and eventually sync.

**Steps:**
1. Click Add Crop Row
2. Type rapidly into fields — Crop, Farm#, Acres — **before the initial POST is complete**
3. Slow the network if needed: DevTools → Network → Throttling → Slow 3G to widen the POST window
4. DevTools → Network: observe POST first, then one PATCH after POST returns (carrying the extra edits you typed during POST)

**Expected:**
- Single POST creates the row
- Follow-up PATCH fires after POST returns with any edits made during POST in-flight
- Final saved state matches what you typed last

**Pass criteria:** No dropped edits; final state on refresh matches last typed values.

- [ ] Pass / Fail: ___ Notes: ___

---

### Section B — Edit-mode page (applications/[id]/data-collection)

---

### Step 13 — Edit-mode parity: blur-to-PATCH works

**Goal:** The edit page uses the same CropPlanForm — confirm it also wires through.

**Steps:**
1. Open an existing app: `/applications/<id>/data-collection`
2. Find the Farm Plan / Crop Plan section
3. Edit a field on an existing row; blur
4. DevTools → Network: observe `PATCH /crop-plan/entries/{id}`

**Expected:**
- PATCH fires, returns 200
- Save-status indicator shows same states as intake wizard
- Refresh → edit persists

**Pass criteria:** Edit-mode page exhibits identical auto-save behavior.

- [ ] Pass / Fail: ___ Notes: ___

---

### Step 14 — Regression check: LandsGrid PATCH still works

**Goal:** C1 fix aligned the PATCH URL; make sure the Lands grid still persists edits.

**Steps:**
1. In the same app, navigate to Lands / Gap Analysis view (wherever LandsGrid renders)
2. Edit a tract field (e.g. FSA farm number or tract acres)
3. Blur / save
4. DevTools → Network: observe PATCH to `/crop-plan/entries/{id}` (new path)

**Expected:**
- PATCH returns 200
- Edit persists on refresh

**Pass criteria:** LandsGrid didn't break from the URL rename.

- [ ] Pass / Fail: ___ Notes: ___

---

## Post-test cleanup

- If test app is disposable: delete via wizard or leave for reuse
- Capture any bug / unexpected behavior findings in this file under a "Findings" section below
- After all steps pass: confirm ready to merge in chat with Thayne

## Findings / bugs discovered during testing

_(add as you go — include which step surfaced it, what you saw, what you expected)_

- None yet

## Decision

- [ ] All pass → merge PR #586
- [ ] Failures exist → document above, decide fix-in-PR vs file follow-up
