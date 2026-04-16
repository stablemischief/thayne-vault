# Skattbók (Product — Stable Mischief)

## What It Is
Norse-themed PWA for expense tracking. Receipt photo → Claude Vision extracts data → saves to Google Sheets + Google Drive. Originally built for James's personal use across 3 entities (Stable Mischief / Sprout & Spindle / Personal). Being packaged for external clients.

## Stack
- Next.js 14+ App Router, TypeScript, Tailwind CSS
- Clerk auth (Google OAuth)
- Anthropic Claude Sonnet (receipt vision extraction)
- Google Sheets API v4 (expense storage) + Google Drive API (receipt images)
- PWA — installable to iOS home screen via Safari (no App Store)
- Vercel deployment

## Key Features
- Camera capture or upload → AI extracts vendor, date, amount, tax, line items
- Dashboard by entity (clan) — monthly or by category (rune)
- CSV export (standard or QuickBooks format)
- Multi-entity: one Google Sheet per entity per year

## Status
- **Pending updates** — improvements identified by James, not yet implemented
- **Renaming planned** — "Skattbók" is too theme-specific for external packaging; new name TBD
- **Target market:** small businesses, solopreneurs wanting lightweight receipt-based bookkeeping

## Repo
- Local: `/Users/jw-dev/Developer/GitHub/Skattbok`
- GitHub org: confirm before Archon dispatch

## Open Work
- [ ] Apply identified improvements (James to specify)
- [ ] Decide on product name for external release
- [ ] Generalize from personal 3-entity setup → configurable for any client
- [ ] Define packaging / distribution / pricing model
