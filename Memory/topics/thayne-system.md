# Thayne System

## What Thayne Is
Thayne is the PRIMARY 2nd brain agent. Runs on Claude Agent SDK / Claude Code. Built April 2026 using Cole Medin's framework.

## Key Differences from Thane
- **Primary interface: Slack** (fully migrated 2026-04-09 — Discord bot unloaded)
- Thayne vault at ~/Developer/Apps/Thayne-Brain/thayne-vault/
- Thayne is NOT Thane — separate sessions, separate memory, parallel operations

## Build Status
- **All 9 phases complete as of 2026-04-05** ✅
- Phase 1 Foundation → Phase 9 Deployment (launchd). Full system live.
- To activate background agents: `bash .claude/scripts/setup_launchd.sh`

## Skills
- 35 skills total: 22 from Thane, 11 from Cole's reference repo, 2 Thayne-specific (direct-integrations, obsidian-vault-structure)
- All Thayne skills at: `~/Developer/Apps/thayne-brain/.claude/skills/`
- Thane's skills (reference, not Thayne's): `~/Developer/Apps/thane/skills/`
  - archon, stable-mischief-skills, market-garden-specialist, beekeeping, icelandic-sheep, linkedin-post, excalidraw-diagram, prd-writer, investment-pitch, archon-review

## Known Open Gaps (as of 2026-04-13)
- **adw-orchestrator not in GitHub integration** — Agrellus repo not monitored by heartbeat. PR/issue alerts won't fire for Agrellus.
- **Slack bot file_shared** — `slack_bot.py` doesn't handle `file_shared` or thread-attached file events. Needs Archon brief. James explicitly rejected path-workaround; full fix required (event handler + download + pass-through).
- **Investment deck in progress** — Deck outline committed (`agfin-deck-outline-v1.md`). Core gap: investment milestone undefined. Next step: finalize milestone → revise Ask + Use of Funds. Skills: `investment-pitch`, `brand-voice`, `pptx-generator`, `excalidraw-diagram`, `bmad-analyst`. See [[entities/agrellus]] Fundraising section.
- **Project Phoenix** — James mentioned this as his favorite current project (2026-04-10). No details provided yet. Ask what it is next session and whether to create a MEMORY.md entry.
- **Oura integration wiring pending** — Integration built 2026-04-10, token in `.env`. Still need to wire into heartbeat and morning brief.
- **Memory index not auto-rebuilding** — index was manually rebuilt 2026-04-11 (386 chunks/51 files). Phase 10a adds hash-diff sync to heartbeat (no embeddings); weekly `--rebuild` cron still pending.
- **pytest harness missing** — no automated tests for heartbeat routing, slack_notifications contract, or prompt↔alias↔channel map invariant. Filed as follow-up after PR #7.

## Security Notes
- `shared.py` THAYNE_BLOCKED_PATTERNS blocks: git push, git push --force, git merge, gh pr merge, coolify, heroku — enforces SOPs in-process
- `.venv` symlinks break on SMB mounts — venv lives at `~/.venvs/thayne-brain` (local filesystem only)

## System Hardening (2026-04-09)
- **Lock contention root cause:** `CLAUDE_CODE_ENTRYPOINT=cli` in `engine.py` caused SDK subprocesses to share `~/.claude/` SQLite state with interactive sessions → deadlocks. Removed 2026-04-09.
- **Session continuity:** Resets at 8 turns or 50% budget ($1.00). Last 6 exchange pairs buffered in-memory, serialized to `.claude/data/state/prior-context-slack-{channel_id}.json` on reset, injected into next session.
- **Vault git repo:** `thayne-vault/` is now a private repo (`stablemischief/thayne-vault`). Auto-syncs every 2 min via `com.thayne-brain.vault-sync` launchd service.
- **Gmail OAuth fixed 2026-04-09** — `run_local_server(port=0)` fix; james@whitfieldjames.com authorized.
- **Archon-check** now runs daily at 9am CST (was weekly Monday).
- **All launchd plists** updated to `/Users/jw-dev/` paths, uv at `/opt/homebrew/bin/uv` — reloaded and verified 2026-04-09.

## System Updates (2026-04-10)
- **PR #2 merged (thayne-brain)** — Archon patched: Slack image upload (with dedup), removed module-level `CLAUDE_INVOKED_BY` that was blocking all hooks inside Agent SDK sessions, reduced `max_turns` default 25→15 for response latency.
- **Oura integration built** — `integrations/oura.py`, `query.py` (oura subcommands), `registry.py` updated. Token delivered via SSH to `.env` (never via chat). Next: wire into heartbeat + morning brief.
- **Mac Mini Tailscale IP:** `100.104.66.125` — James uses this to SSH from iPad (Terminus app). Credential pattern: SSH in → `echo '...' >> .claude/scripts/.env`. Never paste tokens into Slack.

## System Updates (2026-04-12)
- **Heartbeat spam bug fixed** — `apply_heartbeat_fix.py` patched `heartbeat.py` with 3 changes: (1) removed `has_farm_tasks`/`has_attention` from quiet-mode override, (2) narrowed habit nudge to 3–5pm only, (3) per-channel cooldown gate in state file (farm: 24h, morning-briefs: 6h, focus: 20h, email: 2h, alerts: 1h, else: 24h). ✅ **Confirmed applied 2026-04-14.**
- **Operational lesson (uv):** `uv run` must be invoked from `.claude/scripts/` (where `pyproject.toml` lives), not from project root. Venv at `~/.venvs/thayne-brain` was intact — false "package missing" error was a wrong cwd, not a broken venv. Never suggest venv recreation without first confirming working directory.

## System Updates (2026-04-13)
- **PR #7 merged** — Discord-to-Slack migration complete. heartbeat.py now routes all notifications via Slack chat.postMessage. Discord code fully removed (733 lines). 8 missing channels added to SLACK_CHANNEL_MAP.
- **Memory index rebuilt** — first-ever build: 386 chunks across 51 vault files. Semantic search now functional.
- **Heartbeat verified working** — `heartbeat.py --force` confirmed 3 Slack messages delivered (#bees, #morning-briefs, #focus). All 4 launchd agents reloaded.
- **claude-agent-sdk upgraded** — 0.1.56 → 0.1.58.
- **Channel isolation rules added** — CLAUDE.md now explicit: never surface content from another channel, redirect cross-channel topics by name only.
- **VS Code session close rules added** — CLAUDE.md now requires: update relevant vault file AND update MEMORY.md after every VS Code session with a decision or plan change. Both required, no "or".
- **.archon/ gitignored** — Archon worktree artifacts removed from tracking, won't pollute repo going forward.
- **vault_sync_runs.log gitignored** — runtime log was accumulating 1400+ lines per day in git changes.
- **CHAT_MAX_TURNS fixed** — was 25 in .env overriding code default of 15. James manually corrected to 15.
- **Issues #1, #3, #4 closed** — all resolved by PR #1 and PR #2.

## System Events (2026-04-15)
- **401 auth outage:** Heartbeat Claude API calls failed with 401 "Invalid authentication credentials" from ~10:14–14:46 CST (8+ consecutive heartbeats). Self-resolved at 15:16 with no intervention. Root cause unknown — first recorded occurrence. Watch for recurrence.
- **Gmail triage gaps:** Multiple heartbeats flagged promotional emails slipping through auto-label filter (hotel deals, CHD event invites, news blasts, TikTok notifications). Triage logic likely needs broader marketing category coverage.
- **Flush error (isolated):** One `memory_flush.py` failure at 20:47 (exit code 1). Single occurrence — not yet a pattern.
