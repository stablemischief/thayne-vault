# AGRELLUS.md — Deep Context Reference

_Extended memory for the Agrellus / AgFin project. Load when working on Agrellus tasks._
_For quick status, see MEMORY.md. This file carries full strategic + technical depth._

---

## What AgFin Actually Is

AgFin is a **compliance-grade document intelligence platform for agricultural loan origination** — the first product of Stable Mischief. It is NOT just a tool for Agrellus; it is the proof-of-concept for Stable Mischief's entire AI product methodology.

**Core capability:** When documents are uploaded, AgFin classifies them, deploys parallel extraction agents (two AI models reading the same documents simultaneously), compares outputs field by field, and uses an arbitration agent to resolve disagreements. Every decision is logged in an immutable, hash-chain audit trail. Fields below confidence threshold are flagged for human review.

**Scale:**
- 1,395 structured fields across 10 lending modules
- 9 agricultural document types (FSA-578, Schedule F, insurance, balance sheets, driver's licenses, lease agreements, etc.)
- 6-phase AI extraction pipeline
- 91 pipeline-specific tests
- AES-256 PII encryption, row-level security, append-only audit trails — built in from day one

**Build proof:** Built in under 120 days by fewer than 5 people. One of 5 AI products the Stable Mischief team has built in 12 months.

---

## Key Contacts (Agrellus-Specific)

- **Chris Johnson** — formal advisor. CEO of Agrellus (1.5M farmer network). Agrellus Financial is AgFin's first distribution channel.
- **Mark Branch** — Agrellus client contact for field/spec questions.

_For full team bios, investment thesis, three-product portfolio, and patentable innovations → see STABLE-MISCHIEF.md_

---

## Architecture — The Three Non-Negotiable Structural Requirements (V5)

These override all Excel structural limits. Never revisit.

| Requirement | Excel limit | Digital product |
|---|---|---|
| **Unbounded Tracts** | 16 rows | Unlimited (avg 60, max 300+) — `land_tracts[]` |
| **Dynamic Members** | 4 partner slots | Unlimited, polymorphic (individual OR entity) — `members[]` |
| **Dynamic Crops** | Fixed columns | Unlimited — `crops[]` |

Every tract carries: County, Farm Number, Land Owner, Crop, Dry Acres, Irrigated Acres, Lease, Section, Township, Range.

---

## Architecture Audit Findings (2026-03-31 — Three-Agent Parallel Audit)

James and Mitch ran a three-agent parallel audit (Developer + Product/Lender + QA/Adversarial lenses) before writing more code. All three agents agreed on the top 5 findings:

1. **Static slot ceilings** (4 members, 8 crops, 10 debt, 40 equipment, 12 yield rows) silently truncate enterprise data — P5/P6 enterprise scenarios cannot be served
2. **Completion % systematically inflated** — empty strings, zeros, 0%-confidence fields all count as "complete"
3. **Modules 4, 6, 7, 8, 10 not production-ready** — Module 4 (underwriting basis) at ~4% implementation
4. **Dry/irrigated split broken end-to-end** — $1.5–1.8M revenue projection error possible on a single crop
5. **Schedule F missing 7 critical fields** (County, SSN, Spouse SSN, Non-Farm Income, Living Expenses, Capital Expenditures)

**Per-agent unique findings:**
- Developer: SF labor double-counted; interest rate hardcoded at 5%; AVERAGE denominator bug (divides by present count not expected count)
- Product: Production credit info completely missing; PDF export partially broken; P7 corporate/P8 livestock not supported
- QA: FieldDisplay component (349 lines) has zero tests; regression suite tests stability not correctness; duplicate doc upload blocks correction

**Audit files in repo:**
- `jw-planning/Working/implementation-plans/v5-architecture-audit-2026-03-31.md`
- `jw-planning/Working/implementation-plans/synthesis-three-agent-audit-2026-03-31.md`

---

## REC Phase — Remediation Tracking (as of 2026-04-02)

**Final audit tally (Thane + Dex/Archie dual-team):** 29 findings → 19 confirmed, 2 partially correct, 1 redirected, 4 invalidated/closed

**Notable false positives:**
- D8 (duplicate dict keys) — FALSE POSITIVE: two separate dicts in module_service.py
- D10 (BS categorization) — FALSE POSITIVE: BS_equipment/BS_vehicles already correctly categorized
- Issue #333 (REC-03) — CLOSED, false positive

**Key issue status (as of 2026-04-02):**
- #330 (REC-04): branch fix/330-duplicate-key-guard — 1 commit ahead of staging, passes CI, awaiting Mitch to open PR
- #331 (REC-02): expanded to full Module 8 four-block architecture rebuild (still open)
- #349 (REC-13): livestock conditional added (checkbox in Step 3)
- #355 (REC-22): P7 out of scope per Mark Branch; P8 covered by REC-08 + REC-13
- #369: Farm Plan Practice column — PR #390 opened, DRAFT, awaiting Mitch review

**GitHub cleanup (April 1):** Closed PRs #272, #310, #313, #317, #318 (superseded). Tag: `staging-backup-2026-04-01` on HEAD 79acc23.

---

## Calculations Inventory (in progress as of 2026-04-02)

**Module 2 — Lands Farmed (COMPLETE)**
- LM_crop_N_dry_acres: CONDITIONAL from Farm Plan (authoritative) — [TO BE FIXED — currently reads FSA]
- LM_crop_N_irr_acres: CONDITIONAL from Farm Plan — [TO BE FIXED]
- LM_crop_N_total_acres, LM_total_dry_acres, LM_total_irr_acres, LM_total_acres: SUM calculations
- All rows unbounded post-REC-05 (currently capped at 8 slots — [TO BE FIXED])

**Module 3 — Financial Statement (COMPLETE)**
- 13 BS_ calculated fields in 4 dependency layers
- Layer 1: leaf subtotals (current/intermediate/fixed/non-farm assets + liabilities)
- Layer 2: BS_total_assets, BS_total_liabilities
- Layer 3: BS_total_equity = total_assets − total_liabilities
- Layer 4: BS_total_liabilities_and_equity (verification — must equal total_assets)

**Next in queue:** Module 4 (82 PI_ formulas, 8-slot cap), Module 5 (PE_ + OP_), Module 6 (20 SUM_ formulas + DSCR), Module 7 (12 YH_ averages), Module 8 (post-REC-02 rebuild), Module 9 (2 EL_ totals)

---

## Canonical Architectural Decisions (Stable — Do Not Revisit)

- **Module 8:** Schedule F is the SOLE calculation source. SF Part I → HIE_total_farm_income. SF Part II → HIE_total_farm_expense. Analyst detail entries (Blocks 3+4) are annotations only, never calculated.
- **Unbounded structures:** Farms, crops, equipment, leases, financial fields — all unbounded. V5 was a template (Excel origin), not a ceiling.
- **LM dry/irr acres:** Farm Plan (applicant-entered) is authoritative per V5, NOT FSA document extraction.
- **GridView Design Standard:** #2c4a3e header, #c05c00 amber values, 2-column card grid, no zebra striping. Locked in CLAUDE.md on staging.
- **APH source:** Prior year insurance schedule only (e.g., 2025 schedule = years 2019–2024). Structured by farm number + crop + practice.
- **V5 fields #65 (Section), #66 (Township), #67 (Range):** NOT REQUIRED per Mark Branch.
- **Coverage %, liability, premium, historic price:** NOT required this version per issue #265.

---

## POST-REC Feature Queue (do not build until REC is complete)

- **#382** — Balance sheet total validation indicator on calculated fields
- **#383** — Fix BS extraction prompt — extract totals (prerequisite for #382)
- **#384** — Replace fixed BS field schema with relational extraction model (architectural)
- **#385** — Financial document analyst agent (depends on #384)

---

## Key File Paths

| Resource | Path |
|---|---|
| Repo (local) | `/Users/jw-dev/Developer/GitHub/adw-orchestrator/apps/agrellus-crop-finance-mvp` |
| V5 spec | `docs/specs/02-stage-1-data-point-inventory-v5.md` (v6 also added Apr 8) |
| Calculation engine | `api-backend/app/services/calculation_engine.py` |
| Audit synthesis | `jw-planning/Working/implementation-plans/synthesis-three-agent-audit-2026-03-31.md` |
| Company narrative | `/Users/jw-dev/Developer/Apps/thane/Stable-Mischief-Investment/content/company-narrative-v4.md` |
| Investment deck outline | `/Users/jw-dev/Developer/Apps/thane/Stable-Mischief-Investment/deck/agfin-deck-outline-v1.md` |
| Thane Apr 2 notes | `/Users/jw-dev/Developer/Apps/thane/memory/2026-04-02.md` |

---

## The 8 Applicant Profiles (V5 Scenario Taxonomy)

| # | Profile | Entity | Scale |
|---|---------|--------|-------|
| P1 | The Simple Tenant | Individual/Sole Prop | Small (1–10 tracts) |
| P2 | The Family Farmer | Individual/Sole Prop | Mid (11–60 tracts) |
| P3 | The Diversified Operator | Individual/Sole Prop | Large (61–150 tracts) |
| P4 | The Solo LLC | Single-Member LLC | Mid (11–60 tracts) |
| P5 | The Family LLC | Multi-Member LLC (2–5) | Mid-Large (30–150 tracts) |
| P6 | The Enterprise LLC | Multi-Member LLC (6+) | Enterprise (150–300+ tracts) |
| P7 | The Corporate Farm | S-Corp or C-Corp | Mid to Large — **OUT OF SCOPE this version** |
| P8 | The Mixed Operation | Any + Livestock | Mid to Large — livestock overlay for financial context only |

Profile selection dynamically configures the form: entity type → member fields; marital status → spouse fields; scale → table sizing; irrigation → irrigated acre columns; crop count → income/expense columns; off-farm income → non-farm sections; livestock → P8 fields.

---

_Last updated: 2026-04-08 — business thesis, investment context, team origin, and patent innovations moved to STABLE-MISCHIEF.md_
