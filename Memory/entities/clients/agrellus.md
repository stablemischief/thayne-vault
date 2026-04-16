# Agrellus (Client — adw-orchestrator)

> **Full context in:** `thayne-vault/Memory/AGRELLUS.md` — investment thesis, team origin story, architecture audit findings, REC tracker, calculations inventory, 8 applicant profiles.

## What It Is
Compliance-grade document intelligence platform for agricultural loan origination. First product of Stable Mischief. Client: Chris Johnson (CEO, Agrellus).
Stack: Next.js, FastAPI, Supabase (Stable Mischief standards).
Scale: 1,395 fields, 9 doc types, 6-phase pipeline, 91 tests. Built in <120 days by <5 people.

## Current State (as of 2026-04-06 — confirmed by James)
- AgFin is #1 priority — everything else holds until it's caught up (~2 weeks behind)
- Remediation (REC) phase ongoing: 19 confirmed findings, architectural fixes in progress
- Mitch has completed most issues and **all previously open PRs are now merged/closed** ✅
- Active branch: `staging` (HEAD 79acc23)
- Backup tag: `staging-backup-2026-04-01`

## Key People
- Chris Johnson (Agrellus CEO) — final authority on product decisions
- Mitch Dornich — primary builder, reviews and merges PRs for Agrellus
- Mark Branch — client contact for field/spec questions

## Repo
- Repo: `mdornich/agrellus-crop-finance-mvp` (GitHub) — Mitch's repo, NOT Stable-Mischief-AI/adw-orchestrator
- Local: `/Users/jw-dev/Developer/GitHub/adw-orchestrator/apps/agrellus-crop-finance-mvp`
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
