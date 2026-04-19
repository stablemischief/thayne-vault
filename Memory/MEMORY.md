# MEMORY.md — Long-Term Memory Index

_Index only. Read linked pages for detail. Use `memory_search.py` for daily-log content._

## Clients
- [[entities/clients/agrellus]] — AgFin compliance platform; REC phase; investment deck; Chris Johnson
- [[entities/clients/nesvick]] — Daily 5-min NTG podcast; all PRs merged; VPS 187.124.231.206

## Products
- [[entities/products/affirm]] — Joe Dispenza alignment PWA; invite-only; 6 open issues
- [[entities/products/open-notebook]] — Stable Notebook (Stable-Mischief-AI/Stable-Notebook); fully independent; repo extraction from adw-orchestrator pending
- [[entities/products/kowerk]] — Business-first AI tool; ON HOLD since 2026-04-06
- [[entities/products/skattbok]] — Personal finance tool; updates pending; rebranding + packaging planned
- [[entities/products/aiba]] — AI Business Analyst; transforms meeting transcripts → deliverables + ROI; client launches upcoming

## Business
- [[entities/business/stable-mischief]] — Biz-dev, strategy, admin, investments; partner Mitch Dornich

## Personal
- [[entities/personal/sprout-spindle]] — Chickens, Icelandic ewes (CDT Dose 2: Apr 19-26 ⚠️), bees, garden. Corn: two 45' rows going in ~Apr 18–25; interseeding crimson clover + cowpeas. Seedlings (cucumbers, cucamelons, peppers, tomatoes, broccoli) in greenhouse with N deficiency + leggy growth; Garrett Juice @ 1oz/gal bottom-watering is current intervention. Lavender planned for south edge of greenhouse — plant 18–24" beyond drip line, raised bed 4–6" for drainage. **Lambing 2026-04-19:** Ewe 11 = 5yr experienced mom (twins: ram lamb + ewe lamb); Evie = first-time mom (single ewe lamb). Note: initial roster had ages/experience swapped — corrected.
- [[entities/personal/wellness]] — Oura (wiring pending), 5 pillars, habit nudge
- [[entities/personal/contacts]] — Family, partners, network, Wade Burt protocol

## Topics
- [[topics/archon-workflows]] — Workflow decision table + 8-section brief standard
- [[topics/gmail-rules]] — Gmail SOPs + working-style hard rules
- [[topics/thayne-system]] — Build status, gaps, hardening, system updates

## Active Items
- **Heartbeat alerting latency** — Apr 16 17:31 heartbeat flagged "Peter call started 30 min ago"; calendar event wasn't pre-surfaced in time. Worth revisiting pre-call lead-time alerting.
- **Barn camera (Apr 18)** — James uses Blink ecosystem. Plan: power Blink Sync Module 2 in barn via small solar system. Decision pending: Voltaic Systems "Always On" USB Kit (~$79–99) vs. DIY panel + SLA battery. Wi-Fi via roof-mounted repeater at house.
- **Drip Depot fittings (Apr 18)** — Bag of fittings with PPC codes (3212-10-1, 3213-30-1, 3211-30-1) unidentified. Action: pull order confirmation from Gmail. Irrigation purpose (beds vs. greenhouse) also unconfirmed.
- **Greenhouse mycorrhizal fungi (Apr 18)** — James referenced a prior discussion about introducing mycorrhizal fungi into greenhouse regeneration area (said "Maisel fungi"). Context was lost. James needs to brief Thayne on what was discussed and already done.

## Recent Decisions (last 30 days)
- **2026-04-16** — Phase 10a: MEMORY.md → index + entity/topic pages; added core-memories.md, SOUL recall protocol, heartbeat memory-index sync.
- **2026-04-14** — Investment deck: $10M flat AgFin-only; milestone definition is the open gap.
- **2026-04-13** — PR #7 merged: Discord→Slack complete; first memory-index build (386 chunks / 51 files).
- **2026-04-09** — Primary interface = Slack; vault is a private git repo (2-min auto-sync).

## Current System State
- All 9 phases complete. 4 launchd agents: heartbeat, reflection, slack-bot, archon-check.
- Slack primary. America/Chicago, active 07:00–22:00.
- Heartbeat: per-channel cooldowns + memory-index hash-diff sync (no embeddings on hot path).
- Open gaps: see [[topics/thayne-system]] "Known Open Gaps".

_Recall protocol: SOUL.md → "Memory Recall Protocol"._
