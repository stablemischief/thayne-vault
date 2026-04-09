# HEARTBEAT.md — Proactive Monitoring Checklist

_Run during each heartbeat. Check items in order. Only surface what needs attention._
_Keep this file small — it burns tokens every heartbeat._

---

## Checklist

- [ ] **Gmail triage** — New emails since last run at james@whitfieldjames.com. Apply Junk/Receipts labels. Surface any that need James's attention (client, team, personal — not marketing/receipts).
- [ ] **Google Calendar** — Events in next 24–48h. Alert for anything <2h away or important. Check both james@whitfieldjames.com AND james@stablemischief.ai calendars.
- [ ] **Farm calendar** — Check `~/Developer/Apps/Thayne-Brain/thayne-vault/Projects/Farm/farm-calendar.md`. Surface tasks due today or in next 3 days. Priority: sheep CDT Dose 2 window (2026-04-19 to 2026-04-26), nuc arrival prep (2026-04-12), nuc arrival (2026-04-15).
- [ ] **GitHub PRs** — Check open PRs across Stable Mischief repos (Agrellus, Affirm, Nesvick). Surface anything that needs James's attention (new reviews, CI failures, blocked).
- [ ] **Draft management** — Scan `~/Developer/Apps/Thayne-Brain/drafts/active/` for drafts >24h without action. Move stale ones to `expired/`. Note any emails that likely need a reply drafted.
- [ ] **Habits nudge** — If it's after 3pm and any habits pillar from HABITS.md is unchecked, post a gentle nudge in `#wellness-general`.

---

## Channel Routing (Slack)

| Alert Type | Slack Channel |
|---|---|
| Thayne proactive alerts (catch-all) | `#thayne-alerts` |
| Email — james@whitfieldjames.com | `#email-whitfieldjames` |
| Email — james@stablemischief.ai | `#email-stablemischief` |
| Calendar / morning brief | `#morning-briefs` |
| GitHub PRs — Nesvick | `#project-podcast` |
| GitHub PRs — Agrellus (AgFin) | `#project-ag-fin` |
| GitHub PRs — Agrellus (AGHI) | `#project-aghi` |
| GitHub PRs — Affirm | `#affirm` |
| GitHub PRs — Skattbók | `#skattbók` |
| GitHub PRs — experiments / misc | `#experiments` |
| Farm general / multi-category | `#farm-general` |
| Sheep | `#sheep` |
| Bees | `#bees` |
| Garden | `#garden` |
| Chickens | `#chickens` |
| Wellness nudges / habits | `#wellness-general` |
| Oura Ring feedback | `#wellness-oura` |
| Diet tracking | `#wellness-diet` |
| Exercise | `#wellness-exercise` |
| Food / meal tracking | `#food` |

---

## Silence Rules

**Stay quiet (reply HEARTBEAT_OK) when:**
- Late night (23:00–08:00 CST) unless urgent
- James is clearly in a session
- Nothing new since last check
- Last check was <30 minutes ago

**Always surface:**
- Sheep health alerts (CDT deadline, lambing signs)
- Nuc arrival / bee events
- Client emails (Agrellus, Nesvick, Kowerk)
- Calendar events <2h away
- CI failures on active PRs

---

## Heartbeat State

Thayne tracks last-check timestamps in `.claude/data/state/heartbeat-state.json`:

```json
{
  "lastChecks": {
    "gmail": null,
    "calendar": null,
    "farmCalendar": null,
    "github": null,
    "drafts": null
  }
}
```

_(File created by Phase 6 scripts — placeholder until then)_
