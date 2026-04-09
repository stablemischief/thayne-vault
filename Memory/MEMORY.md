# MEMORY.md — Long-Term Memory

_Curated knowledge that persists across sessions. Load only in main sessions with James._
_Keep this concise — it loads into every session. Max ~300 lines. No transcripts._

---

# 🌐 GLOBAL — People, Rules, Tooling

## Key Contacts & Emails
- **Jessie Whitfield** (wife; DOB 27Oct1986) — jessfogg@gmail.com
- **Kaya Laine Whitfield** (oldest daughter; DOB 26May2004) — kayalaine@gmail.com
- **Mitch Dornich** (business partner) — mitch@stablemischief.ai
- **Tristan Cuevas** (pro bono dev/learner) — tristan.cuevas.development@gmail.com
  - Works on AgFin. VPS: `ssh tristan@82.29.153.201`
- **Chris Johnson** (Agrellus CEO) — exits of $4B + $1B, introduced James to institutional fundraising
- **Jesse** (Affirm alpha tester) — jesterlm13@gmail.com — met James at Joe Dispenza Nashville weeklong Sept 2025
- **Emmalene Whitfield** (daughter; DOB 15April2016) — family
- **Oaken Whitfield** (son; DOB 13Mar2021) — family; plays soccer
- **Greg Stoner** — met James 2026-04-06 @ Waldo's Chicken & Beer (Nashville). Role/context TBD.
- **Nashville Ag Club** — James attended Meet & Greet 2026-04-06. Networking group, likely relevant to Agrellus/AgFin.

## Email Accounts
- james@whitfieldjames.com — Gmail API + modify access via service account (PRIMARY)
- james@stablemischief.ai — Calendar only (NO Gmail delegation — OFF LIMITS for email)
- Service account: thane-980@thane-calendar.iam.gserviceaccount.com
- Key file: ~/Developer/Apps/thane/secrets/google-calendar-sa.json

## Gmail Inbox Rules (NON-NEGOTIABLE)
- Label only. NEVER trash/delete unless James says so explicitly in that moment
- Apply **Junk** to mass marketing; **Receipts** to order/payment emails
- Skip emails with attachments, financial senders, personal senders (Jessie, church)
- Never combine label + trash in one run

## Voice Responses — Global Rule
**NEVER generate or send audio unless James explicitly asks.**
Triggers: "read that to me", "say that", "voice reply", "audio version", "speak that"

## Working Style (NON-NEGOTIABLE)
- NEVER push directly to `main` — always branch + PR, James reviews and merges
- NEVER make direct code changes to project repos — Thayne orchestrates, Archon codes
- Before ANY external action: say what I plan to do, explain repercussions, wait for approval
- Only acceptable direct edits: Thayne's own workspace (~/Developer/Apps/thayne-brain)
- **NEVER merge PRs — James merges. Always.**
- **NEVER push code directly — Archon codes, James merges.**
- **NEVER deploy — James deploys via Coolify. Always.**

---

# 🤖 ARCHON — Workflow Rules (NON-NEGOTIABLE)

## Workflow Selection — Use the RIGHT one every time

| Task | Workflow |
|------|----------|
| Research / validate only (no code) | `archon-assist` (no --branch) |
| Have a plan, need code written | `archon-plan-to-pr` |
| Natural language feature, no plan | `archon-idea-to-pr` |
| Fix a GitHub issue | `archon-fix-github-issue-dag` |
| Review a PR | `archon-smart-pr-review` |
| Full 5-agent PR review | `archon-comprehensive-pr-review` |
| Architecture/cleanup | `archon-architect` |
| PRD, short (≤7 stories) | `archon-ralph-stateful` |
| PRD, long (7+ stories) | `archon-ralph-fresh` |

**`archon-assist --branch` NEVER pushes to GitHub.** Using it for code = broken deploys.
**Root cause of 2026-03-29 production outage** — never repeat this.

## After Every Archon Run
Verify push landed: `git -C <worktree> log --oneline origin/<branch>..HEAD`
Must show 0 unpushed before telling James "ready to deploy."

## Archon Brief / Issue Quality Standard (MANDATORY)

Every GitHub issue sent to Archon must include all 8 sections:
1. **Problem Statement** — what fails/is missing, exact errors, file paths, function names
2. **Proposed Solution** — exact files to change, before/after, function signatures
3. **Approved Mockup** (UI issues) — FULL HTML embedded as code block, not a file path
4. **Code Examples** — concrete before/after snippets, type definitions, JSX/Python
5. **Key Files to Reference** — every file Archon should read before touching anything
6. **Acceptance Criteria** — numbered checklist, each item verifiable, tests pass before AND after
7. **What NOT to Change** — explicit list of files and behaviors that must remain untouched
8. **Research Findings** — facts Thayne observed: code snippets, line numbers, what was ruled out

**Show the complete issue to James and WAIT for explicit "go ahead" before dispatching Archon.**

---

# 📦 PROJECT: Agrellus (Client — adw-orchestrator)

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
- Repo: `adw-orchestrator` (GitHub)
- Local: `/Users/jw-dev/Developer/GitHub/adw-orchestrator/apps/agrellus-crop-finance-mvp`
- V5 spec: `docs/specs/02-stage-1-data-point-inventory-v5.md` (v6 added 2026-04-08)

## Architectural Decisions (Stable — Do Not Revisit)
- Module 8: Schedule F is sole calculation source. SF Part I → HIE_total_farm_income. SF Part II → HIE_total_farm_expense.
- All repeating structures: unbounded post-REC-05. V5 = non-negotiable requirement.
- LM dry/irr acres: Farm Plan (applicant-entered) is authoritative source per V5, not FSA doc extraction.
- GridView Design Standard: #2c4a3e header, #c05c00 amber values, 2-column card grid, no zebra striping.


---

# 📦 PROJECT: Affirm (Dr. Joe Dispenza Alignment Tool)

## What It Is
Personal alignment PWA (mobile-first, iOS-first). Generates personalized affirmations, teachings, future memories, energy center clearings. Invite-only. Grounded in Dr. Joe Dispenza + Florence Scovel Shinn.

## Repo & Hosting
- **GitHub:** https://github.com/stablemischief/affirm (private)
- **Local:** ~/Developer/GitHub/affirm
- **Live:** https://affirm-seven.vercel.app
- **Vercel:** Pro plan ($20/mo) for 60s function timeout
- **Supabase project:** gehusargncaruduwvixl

## Stack
- Next.js App Router + TypeScript + Supabase Auth + Anthropic Claude API + PWA

## Current State (as of 2026-04-06 — confirmed via GitHub)
- 50 PRs merged total
- **No open PRs**
- **Open issues:**
  - #48 — feat: Admin panel (manage waitlist, invites, users, fees)
  - #49 — feat: In-app feedback submission from Settings
  - #51 — Account Logout Function
  - #52 — fix: futureMemories duplication in mergeConfigs + gender-neutral
  - #53 — Admin Panel: User Model Token Use
  - #54 — feat: Audit logging (config mutations + API errors)
- **Previously open branches — all merged:**
  - `archon/task-feat-meditation-recommendation` → PR #24 merged
  - `archon/task-fix-invite-signup` → PR #25 merged
  - PR #30 (Enter key + conversation persistence) → merged 2026-03-31
  - PR #31 (daily affirmation persistence) → merged
  - PR #50 (waitlist/Request Access flow) → merged 2026-04-02

## Alpha Testers
- **Jesse** (jesterlm13@gmail.com) — positive feedback; Enter key + conversation issues resolved
- **Jessie Whitfield** (jessfogg@gmail.com) — invite signup fix merged (PR #25)

## Approved Product Description
"Each time you open it, it generates a custom affirmation, a short Joe teaching, a future memory exercise, and an energy center clearing — all personalized to your specific intentions and inner work. It can also recommend which Joe meditation to practice based on how much time you have."

---

# 📦 PROJECT: Nesvick Podcast (open-notebook-nesvick)

## What It Is
Daily 5-minute spoken audio podcast from NTG Morning Comments newsletter.
Client: Nesvick Trading Group. Two hosts: Zachary Davis + Riley Chen.
Live: https://nesvick.podcast.stablemischief.ai

## Current State (as of 2026-04-06 — confirmed by James)
- **All PRs merged** ✅ — James confirmed all Nesvick repo PRs are merged as of 2026-04-06
- PR #22 (shareable episode URL): MERGED ✅
- PR #33 (transcript reviewer agent + simplified prompt): MERGED 2026-04-01
- PR #35 (force all segments to short turn count — 3 turns): MERGED 2026-04-01
- PR #36 (sentence-opening conjunction rule): MERGED ✅
- **TODO:** Switch Coolify to main, update seed script, backport to Stable-Notebook

## Critical Decisions (PROVEN — Do Not Revisit)
- **Turn count = the ONLY reliable length lever** — word count instructions in templates are unreliable
- **enforce_transcript_length() must be deleted** — it cuts from the end, destroys content
- Never edit podcast_creator library files directly — override via our code

## VPS
- IP: `187.124.231.206` | SSH: `root`
- Coolify UI: `http://187.124.231.206:8000`

---

# 📦 PROJECT: Kowerk (Stable Mischief own product)

- Business-first AI interaction tool
- Conversational interface: chat, artifact creation, MCP/plugin integrations
- **Status: ON HOLD as of 2026-04-06** — deprioritized until AgFin (Agrellus) functionality is caught up. Product is ~2 weeks behind. No active development until then.

---

# 🏡 FARM: Sprout & Spindle

## Livestock Summary
- **Chickens:** 6 laying hens, 2 roosters, ~19 chicks (moved to coop brooder ~March 6)
- **Sheep:** 2 Icelandic ewes — Evie (EWE-01, 5yr, experienced, pregnant ~April lambing) and 11 (EWE-02, 2yr, first-time mother, pregnant ~May lambing). Arrived March 13, 2026.
- **Bees:** 2 existing honeybee colonies (medium box added March 6). 2 nucs arriving ~April 15 from Mark Menz.

## Upcoming Farm Tasks (as of 2026-04-05)
- **2026-04-12:** Stage equipment for nuc arrival
- **2026-04-15:** Nuc arrival from Mark Menz (2 nucs, overwintered queens)
- **2026-04-19 to 2026-04-26:** CDT Vaccine Dose 2 for Evie & 11 (5mL each — CRITICAL WINDOW)
- **TBD:** Baseline FAMACHA score + fecal egg count for both ewes
- **TBD:** Establish large animal vet — critical before lambing

## Sheep Health (current as of 2026-03-29)
- CDT Dose 1 given: 2026-03-29, both ewes
- CDT Dose 2 window: 2026-04-19 to 2026-04-26 (DO NOT MISS)
- No FAMACHA scores yet — baseline pending
- Evie: ~April lambing. 11: ~May lambing. Both pregnant, monitor closely.

## Garden / Greenhouse
- Greenhouse seedlings: cucumbers (Beit Alpha + Cool Customer), cucamelons, tomatoes (Cherry + Homestead), broccoli
- Carrot + beet bed (right of greenhouse): planted March 21, straw mulch applied, grain sprouts germinating from straw (pull them, real seeds still viable)
- Broccoli: 30 seedlings, should be transplanted outdoors (cool-season, NOT greenhouse)
- Spring ramp-up underway

---

# 🛠️ GLOBAL: Skills Reference
All Thayne skills at: `~/Developer/Apps/thayne-brain/.claude/skills/`
_(Populated as phases complete)_

Thane's skills (reference, not Thayne's): `~/Developer/Apps/thane/skills/`
- archon, stable-mischief-skills, market-garden-specialist, beekeeping, icelandic-sheep, linkedin-post, excalidraw-diagram, prd-writer, investment-pitch, archon-review

---

# 🧠 THAYNE SYSTEM

## What Thayne Is
Thayne is the PRIMARY 2nd brain agent. Runs on Claude Agent SDK / Claude Code. Built April 2026 using Cole Medin's framework.

## Key Differences from Thane
- **Primary interface: Slack** (transitioning from Discord as of 2026-04-06 — James confirmed)
- Discord (Thayne Brain server, Guild ID: 1490527019363078185) still configured for heartbeat/alerts
- Thayne vault at ~/Developer/Apps/thayne-brain/thayne-vault/
- Thayne is NOT Thane — separate sessions, separate memory, parallel operations

## Discord Bot
- Application: "Thayne Brain"
- Application ID: 1490521539026751630
- Guild ID: 1490527019363078185
- Bot token: in James's notes (NOT in repo)
- Status: Bot joined server ✅. Permissions fix: remove + re-invite with int 117880 (invite URL in 2026-04-05 log)
- **Message Content Intent must be enabled** in Discord Developer Portal for bot to read message content

## Build Status
- **All 9 phases complete as of 2026-04-05** ✅
- Phase 1 Foundation → Phase 9 Deployment (launchd). Full system live.
- To activate background agents: `bash .claude/scripts/setup_launchd.sh`

## Known Open Gaps (as of 2026-04-06)
- **Gmail auth broken** — `google_token.json` missing. Fix: `uv run python .claude/scripts/setup_auth.py` (browser flow, one-time).
- **adw-orchestrator not in GitHub integration** — Agrellus repo not monitored by heartbeat. PR/issue alerts won't fire for Agrellus.

## Skills
- 35 skills total: 22 from Thane, 11 from Cole's reference repo, 2 Thayne-specific (direct-integrations, obsidian-vault-structure)

## Security Notes
- `shared.py` THAYNE_BLOCKED_PATTERNS blocks: git push, git push --force, git merge, gh pr merge, coolify, heroku — enforces SOPs in-process
- `.venv` symlinks break on SMB mounts — venv lives at `~/.venvs/thayne-brain` (local filesystem only)
