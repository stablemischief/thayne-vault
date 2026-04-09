---
project: WorkingDocs
source: thane
imported: 2026-04-07
---
# Email Monitoring Plan
_Created: 2026-03-06 | Status: Draft — parked for future implementation_

---

## Goal
Triage two inboxes (james@stablemischief.ai and james@whitfieldjames.com) so James only sees what matters, when it matters — without AI reading email body content (prompt injection risk mitigation).

---

## Security Approach: Rules-First, AI-Later

**Phase 1 (now):** Gmail filter rules based on sender/subject/domain — zero AI, zero injection risk.
**Phase 2 (later):** Optional AI layer that classifies on metadata only (sender + subject line, never body). James reviews and acts; AI never takes autonomous action.

---

## Folder Structure (apply to both inboxes)

| Folder | Purpose |
|--------|---------|
| **Inbox** | Only things requiring immediate attention |
| **📢 Marketing & Promotions** | Newsletters, service promos, SaaS updates — review/bulk delete daily |
| **⚡ Action Required** | Clients, contractors, alerts, anything needing a response |
| **📋 FYI** | Receipts, confirmations, automated notifications — good to know, no action needed |
| **🗂️ Archive** | Processed/done |

---

## james@stablemischief.ai Rules

### Rule 1 → Marketing & Promotions
**Criteria (skip inbox, apply label "📢 Marketing & Promotions"):**
- From domains: `@mailchimp.com`, `@sendgrid.net`, `@klaviyo.com`, `@hubspot.com`, `@constantcontact.com`, `@campaignmonitor.com`, `@marketo.com`
- Subject contains: `unsubscribe`, `newsletter`, `% off`, `deal`, `sale`, `free trial`, `webinar`, `you're invited`, `join us`, `limited time`, `offer expires`
- Category: **Promotions** (Gmail auto-tab — enable and label)

### Rule 2 → FYI (Receipts & Notifications)
**Criteria (skip inbox, apply label "📋 FYI"):**
- From: `noreply@*`, `no-reply@*`, `notifications@*`, `alerts@*`, `billing@*`, `receipts@*`, `support@*` (automated senders)
- Subject contains: `receipt`, `invoice`, `your order`, `payment confirmation`, `subscription renewed`, `your account`, `password reset` _(note: password resets should stay in inbox — see exception below)_
- **Exception:** Any subject containing `password reset` or `security alert` → stays in **Inbox**

### Rule 3 → Action Required
**Criteria (apply label "⚡ Action Required", keep in inbox):**
- From known client domains (add as you go): `@agrellus.com`, `@[client domains]`
- From: Mitch Dornich
- Subject contains: `urgent`, `ASAP`, `action needed`, `response requested`, `deadline`, `contract`, `proposal`, `invoice` (from humans, not automated)

### Rule 4 → Catch-all
Anything not matching above stays in **Inbox** by default.

---

## james@whitfieldjames.com Rules

### Rule 1 → Marketing & Promotions
Same domain/subject criteria as above, plus:
- Any sender where James has previously clicked "unsubscribe" (Gmail remembers these)
- Amazon, retail, subscription service promotional emails

### Rule 2 → FYI
Same as above, plus:
- School/activity notifications for kids (identify sender domains once known)
- Bank/financial notifications that are informational only

### Rule 3 → Action Required
- Family-related emails requiring response
- Professional contacts outside Stable Mischief
- Anything from `@[known important personal contacts]`

### Rule 4 → Personal Inbox
- Family (Jessie, Kaya, close friends)
- Anything that doesn't match above

---

## How to Set Up Gmail Filters

### Step-by-step (do this in each inbox):

1. Open Gmail → Settings (gear icon) → **See all settings**
2. Go to **Filters and Blocked Addresses** tab
3. Click **Create a new filter**
4. Enter criteria (From / Subject / etc.)
5. Click **Next** → choose action:
   - ✅ Skip the Inbox (Archive it)
   - ✅ Apply the label: [choose folder]
   - ✅ Also apply to matching conversations (to catch existing mail)
6. Click **Create filter**
7. Repeat for each rule

**Tip:** You can import/export filters as XML. Once the first inbox is configured, export and adapt for the second.

---

## Phase 2: AI Triage Layer (Future)

When ready to add AI on top of the rules:

**What it does:**
- Runs on a schedule (every 30–60 min)
- Reads **subject line + sender only** from "Action Required" folder (never body content)
- Classifies priority: High / Medium / Low
- Generates a daily digest brief: sender, subject, one-line context, suggested action
- Delivers digest to James via Discord or email
- James replies to digest to trigger any action — AI never acts autonomously on email

**What it never does:**
- Read full email body content (prompt injection prevention)
- Forward, delete, or send emails without explicit instruction
- Act on instructions found inside email content

**Tech approach:**
- IMAP read access via App Password (separate from Thane's Gmail account)
- Strict tool guardrails: read metadata only, move to folder, no send/forward
- All actions logged

---

## Open Questions (to resolve before Phase 2)

- [ ] What domains host james@stablemischief.ai and james@whitfieldjames.com? (Google Workspace, custom IMAP?)
- [ ] App Password setup for each inbox
- [ ] Confirm client domain list for "Action Required" rules
- [ ] Who covers farm/family comms while James is traveling? (affects routing priority)
- [ ] Decide: digest via email or Discord?

---

## Status
- [ ] Phase 1: Gmail rules configured for stablemischief.ai
- [ ] Phase 1: Gmail rules configured for whitfieldjames.com
- [ ] Phase 2: AI triage layer (parked — revisit when ready)
