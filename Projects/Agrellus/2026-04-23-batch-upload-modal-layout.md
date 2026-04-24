---
date: 2026-04-23
project: Agrellus / agrellus-crop-finance-mvp
issues: [#556, #585]
prs: [#582, #597]
status: merged to staging
---

# Batch Upload Modal Layout Fixes

## Original defects (issue #556)

Three defects in the document batch upload modal:
- **Defect A** — 6th stat card ("Failed") partially clipped by modal's `overflow-hidden`
- **Defect B** — Status labels ("Converting...", "Classifying...") cut off on the right edge of file rows during processing phase
- **Defect C** — Upload Preview dialog overflowed viewport for Insurance uploads, hiding the "Start Upload" button entirely (only Ins — FSA/Lease filenames were short enough to stay under the tipping point)

## What shipped

### PR #582 (merged)
Archon via `archon-fix-github-issue-dag`. Tailwind class-level fixes in two files:
- Modal widened `sm:max-w-2xl` → `sm:max-w-3xl`
- Preview filepath `truncate` → `break-all` (solved Defect C)
- Doc type span → `min-w-0 max-w-[160px] truncate sm:inline-block`
- Scroll container → `overflow-x-hidden`
- Two hand-pushed follow-ups: `min-w-0` on BatchProgressTracker root and the `<li>`

Defect C fully resolved. Defects A/B still presented in testing.

### PR #597 (merged)
Direct fix for remaining Defect B. Converted the file-status row from flex to CSS grid with explicit `minmax(0, 1fr)` (filename) and `minmax(0, 10rem)` (doc type) tracks. Doc type span now renders unconditionally (empty when missing) so the 4-column `sm:` grid stays stable.

## Technical takeaway — flex min-content propagation in shadcn DialogContent

Inside `DialogContent` (shadcn uses `grid gap-4 ... p-6`), multiple layers of `min-w-0` on the flex row and its ancestors were still not enough to force the filename to truncate once the doc type column rendered. The min-content chain from the unbreakable underscore-filled filename propagated up through the flex row to the grid item, and the grid item refused to shrink below that min-content despite `min-w-0`.

**Deterministic pattern for row layouts inside shadcn DialogContent:** use CSS grid with `grid-cols-[auto_minmax(0,1fr)_minmax(0,<max>)_auto]` instead of flex + `min-w-0` gymnastics. The `minmax(0, ...)` tracks explicitly allow shrink-to-zero, sidestepping the intrinsic-sizing issue entirely.

## Rabbit holes worth noting

- **`rm -rf .next` while the dev server is running** — left orphan node processes on port 3000 serving broken/incomplete output. Bun's `next dev` refused to start because the old PID still held the port. Cleanup: `kill <pid>` (or `lsof -ti :3000 | xargs kill`) before restart.
- **Archon's CLAUDE.md rule** (`Do NOT commit or push`) conflicts with `archon-fix-github-issue-dag`'s `/fix-all` self-fix phase. Archon correctly stopped at commit boundary and reported — the uncommitted self-fix test file lived in the worktree until we manually committed + pushed it. Worth knowing when cleaning up worktrees post-run: check for uncommitted additions first.
- **Tailwind arbitrary values (`max-w-[160px]`)** compiled fine under Turbopack — not the issue. The issue was layout semantics, not CSS emission.

## Follow-up

None. Both PRs merged to staging. Issue #556 closed by #582 merge. Issue #585 closed by #597 merge.
