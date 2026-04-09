# HEARTBEAT.md — Proactive Monitoring Checklist

_Run during each heartbeat. Check items in order. Only surface what needs attention._
_Keep this file small — it burns tokens every heartbeat._

---

## Checklist

- [ ] **Gmail triage** — New emails since last run at james@whitfieldjames.com. Apply Junk/Receipts labels. Surface any that need James's attention (client, team, personal — not marketing/receipts).
- [ ] **Google Calendar** — Events in next 24–48h. Alert for anything <2h away or important. Check both james@whitfieldjames.com AND james@stablemischief.ai calendars.
- [ ] **Farm calendar** — Check `~/Developer/Apps/thane/projects/sprout-and-spindle/farm-calendar.md`. Surface tasks due today or in next 3 days. Priority: sheep CDT Dose 2 window (2026-04-19 to 2026-04-26), nuc arrival prep (2026-04-12), nuc arrival (2026-04-15).
- [ ] **GitHub PRs** — Check open PRs across Stable Mischief repos (Agrellus, Affirm, Nesvick). Surface anything that needs James's attention (new reviews, CI failures, blocked).
- [ ] **Draft management** — Scan `~/Developer/Apps/thayne-brain/drafts/active/` for drafts >24h without action. Move stale ones to `expired/`. Note any emails that likely need a reply drafted.
- [ ] **Habits nudge** — If it's after 3pm and any habits pillar from HABITS.md is unchecked, post a gentle nudge in `#focus` Discord channel.

---

## Channel Routing

| Alert Type | Discord Channel |
|---|---|
| Email alerts | `#whitfieldjames-email` |
| Farm general / bees | `#sprout-and-spindle` |
| Garden / greenhouse | `#sproutspindle-garden` |
| Sheep (Evie & 11) | `#sproutspindle-sheep` |
| Chickens / flock | `#sproutspindle-chickens` |
| GitHub / Archon / PRs | `#stable-mischief` |
| Morning brief / calendar | `#morning-briefs` |
| Wellness nudges | `#focus` |

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
