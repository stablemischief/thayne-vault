# Agrellus (Client — adw-orchestrator)

> **Full context in:** `thayne-vault/Memory/AGRELLUS.md` — investment thesis, team origin story, architecture audit findings, REC tracker, calculations inventory, 8 applicant profiles.

## What It Is
Compliance-grade document intelligence platform for agricultural loan origination. First product of Stable Mischief. Client: Chris Johnson (CEO, Agrellus).
Stack: Next.js, FastAPI, Supabase (Stable Mischief standards).
Scale: 1,395 fields, 9 doc types, 6-phase pipeline, 91 tests. Built in <120 days by <5 people.

## Current State (as of 2026-04-28)
- AgFin is #1 priority — everything else holds until it's caught up
- Remediation (REC) phase ongoing: 19 confirmed findings, architectural fixes in progress
- **Issue #578 Step 4 (Other Farm Income) fully shipped** — Option C: single JSONB column, 6 PI_* columns dropped, IncomeGrid dynamic rendering. PRs #608–#611 all merged.
- **Issue #613 filed** — per-step PATCH on Step 4 Next click (currently only persists on final Submit). Awaits validate-before-build.
- **Step 2 persistence bug discovered** — multi-county adds don't persist to DB after initial app creation. Pre-existing, not yet filed. P2 follow-up.
- **GitHub Actions billing failure** — CI jobs cancelled across repo. James needs to fix in Billing & Plans settings before any new PRs can merge with green checks.
- Active branch: `staging`
- Backup tag: `staging-backup-2026-04-01`

## Process Lessons
- **Verify migration is live BEFORE merging code PR** on shared-DB setups. Apr 28 outage: PR #610 merged but migration `8e9f0a1b2c3d` was never applied → 500 on `/applications/` for all users. Fixed by applying DDL via Supabase SQL Editor. Backup table `applications_pre_578_backup_20260428` (457 rows) — park ~7 days then drop.
- **Hybrid mockup approach for Archon briefs** — commit interactive HTML mockup to repo as a file Archon can inspect + embed strategic spec blocks inline in issue (color tokens, JSON shape, copy strings) + reference existing production component as implementation pattern + strict shadcn-only AC. Avoids "Archon doesn't read the file" AND "Archon transliterates raw HTML" risks.
- **Single focused mockup > 3 variants** when visual precedent already exists in codebase. Faster review, less ambiguity.
- **Validate-before-build rule** — third consecutive day (Apr 28) with zero fix-test-fix loops on shipped PRs.

## Key People
- Chris Johnson (Agrellus CEO) — final authority on product decisions
- Mitch Dornich — primary builder, reviews and merges PRs for Agrellus
- Mark Branch — client contact for field/spec questions

## Repo
- Repo: `mdornich/agrellus-crop-finance-mvp` (https://github.com/mdornich/agrellus-crop-finance-mvp.git) — Mitch's repo, standalone (NOT under adw-orchestrator monorepo)
- Local: `~/Developer/GitHub/agrellus-crop-finance-mvp`
- V5 spec: `docs/specs/02-stage-1-data-point-inventory-v5.md` (v6 added 2026-04-08)

## Architectural Decisions (Stable — Do Not Revisit)
- Module 8: Schedule F is sole calculation source. SF Part I → HIE_total_farm_income. SF Part II → HIE_total_farm_expense.
- All repeating structures: unbounded post-REC-05. V5 = non-negotiable requirement.
- LM dry/irr acres: Farm Plan (applicant-entered) is authoritative source per V5, not FSA doc extraction.
- GridView Design Standard: #2c4a3e header, #c05c00 amber values, 2-column card grid, no zebra striping.

## Related: Fundraising (Stable Mischief / AgFin Investment Deck)

### Status (as of 2026-04-14)
- **Raise target:** $10M flat, AgFin-only (Deck 1, 8-slide structure)
- **Pivot framing locked:** "compliance-grade document intelligence platform for agricultural lending"
- **Deck outline:** `Thayne-brain/Docs/agfin-deck-outline-v1.md` (committed)
- **Source doc:** `Thayne-brain/Docs/Stable_Investment-Thane-Discussion.txt`

### Confirmed Numbers
- Mark Branch validated: 40-hour processing estimate + $1,000–$1,200 labor cost per application ✅ (cleared to use in deck)

### Open Framing Gap — Investment Milestone
- VC advisor asked "what is your investment milestone?" — James had no answer (2026-04-14). This is the core gap.
- **Milestone = the specific proof point that removes the biggest investor risk and justifies the next raise at a higher valuation.**
- Smaller-raise approach being considered (12–18 month runway) vs. Chris Johnson's "raise big once" advice.
- Milestone candidates on the table: revenue (X ARR), distribution (N associations live), SOC 2 compliance unlock, IP filing, volume proof (applications processed).
- **Next step:** Finalize milestone definition → size the raise → revise Ask + Use of Funds slides.
