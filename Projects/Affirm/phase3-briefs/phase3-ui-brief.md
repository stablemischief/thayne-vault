---
project: Affirm
source: thane
imported: 2026-04-07
---
# Archon Diagnostic Brief — Phase 3: UI Rebuild to Match Final Mockup

## 1. Problem Statement

The current home screen and all action result screens are functionally wired but visually and architecturally far from the approved final mockup (`design/mockups/concept-final-hybrid.html`). Specific failures:

1. All action results render in-place on the home screen — there is no navigation to dedicated result screens
2. No back button exists anywhere in the app
3. The page title and URL never change regardless of which action is active
4. The home screen UI does not match the mockup — missing greeting, wrong tile styling, no icons on tiles
5. Each result screen (Affirmation, Daily Joe, Remember My Future, Alignment) is unstyled relative to the mockup
6. Affirmation and "Today's Blueprint" on home call the same action and produce identical content — they should be distinct presentations of the same data
7. No meaningful loading state — users wait 10–15 seconds with no feedback
8. The Feeling Out of Alignment flow shows input form and full response on one screen — mockup has two stages: input + energy card preview → full response

## 2. Research Findings

### Current architecture
- All action handling lives in `src/app/(app)/home/home-content.tsx` — a single client component with a single `result` state
- Clicking any action tile calls `handleAction(action)` and updates `result` in-place on the same page
- URL stays at `/home` for all states
- No routing between screens

### Mockup architecture (what it should be)
The mockup (`design/mockups/concept-final-hybrid.html`) shows 6 distinct screens navigated client-side:
1. **Home** — greeting + Today's Blueprint affirmation card + 4 action tiles
2. **Affirmation result** — back button, screen title "Affirmation", pull quote in Lora, source badge, context card, footer buttons
3. **Daily Joe result** — back button, "Daily Dose of Joe", source badge, pull quote, "Why this matters" card, footer
4. **Remember My Future result** — back button, "Remembering Your Future", dark green header card, memory text in Lora, emotion tag pills, full-width button
5. **Feeling Out of Alignment (input + energy card)** — back button, "Coming Back to Alignment", textarea, Signal-style energy center card, two-button footer
6. **Alignment response** — back button, "Your Way Back", reframe card, exercise card, clearing card, full-width button

### Design system (from mockup CSS — use these values exactly)
- **Background:** `#FAF7F2`
- **Cards:** `background: white`, large cards `border-radius: 24px`, smaller `18-20px`, `box-shadow: 0 2px 16px rgba(80,60,40,0.07), 0 0 0 1px rgba(80,60,40,0.04)`
- **Primary font:** Lora (serif) — `var(--font-display)` — for pull quotes, memory text, exercise text — 20-22px, line-height 1.56
- **UI font:** Plus Jakarta Sans — `var(--font-ui)` — for labels, buttons, badges
- **Sage green:** `#5C7A5C` (filled buttons), tile icon bg `#EDF3E8`, emotion tags bg `#EDF3E8` color `#4A6B4A`
- **Amber gold:** `#D4A853` / `#B8892A` — featured tile border, exercise badges, center badges
- **Text primary:** `#2D2420` | **Text secondary:** `#6B5F54` | **Text caption:** `#B0A494` / `#C4B8A8`
- **Border:** `#E8E0D4` / `#E0D8CE`
- **Back button:** `44x44px, background: white, border: 1.5px solid #E8E0D4, border-radius: 13px`
- **Filled button:** `background: #5C7A5C, color: white, border-radius: 14px, font-weight: 600, min-height: 48px`
- **Outline button:** `border: 1.5px solid #E0D8CE, background: white, color: #8A7B6B, border-radius: 14px`
- **Source badge (green):** `background: #EDF3E8, color: #4A6B4A, border-radius: 20px, padding: 6px 12px, font-size: 11px`
- **Source badge (amber):** `background: #FFF3CD, color: #B8892A`
- **Tile:** `background: white, border-radius: 20px, shadow, border: 1.5px solid transparent, min-height: 96px, padding: 18px 16px, display: flex, flex-direction: column, gap: 10px`
- **Featured tile (Remember My Future):** `background: linear-gradient(145deg, #F7F3EC, #F2EDE4), border-color: #D4B87A`, title color `#7A5A1A`
- **Tile icon:** `34x34px, border-radius: 10px` — green `#EDF3E8`, sage `#E9EFEA`, amber `#FDF5E0`, terra `#F0EDE8`
- **Memory header card:** `background: linear-gradient(135deg, #4A6B4A, #3D5C3D), border-radius: 20px, padding: 20px 22px`
- **Energy card header:** `background: linear-gradient(135deg, #F5F0E8, #EDE8DF)`, center badge `background: linear-gradient(135deg, #B8892A, #D4A853), 38x38px, border-radius: 11px`
- **Clearing card:** `background: linear-gradient(135deg, #EDF3E8, #E8EFEA), border-radius: 18px`
- **Reframe card:** white, border-radius 18px, reframe text Lora 18px
- **Exercise card:** white, border-radius 18px, exercise text Lora 17px italic

### Route structure (implement as Next.js App Router pages)
- `/home` — rebuild home screen UI (existing page)
- `/home/affirmation` — new page
- `/home/daily-joe` — new page
- `/home/future-memory` — new page
- `/home/alignment` — new page (handles both input stage and response stage)

Each result page calls `/api/generate` on mount with the appropriate action. Do not pass content through URL params.

### Greeting logic
Mockup shows "Good morning, James" (time-of-day aware, client-side). Use `userConfig` for the name — if no name field exists in the type, use "James" as fallback. Time-of-day: morning (5am–12pm), afternoon (12pm–5pm), evening (5pm–5am).

### Today's Blueprint vs Positive Affirmation
Both call `affirmation` action — content will differ per call (Claude generates fresh each time). The difference is presentation:
- **Today's Blueprint** (home card): compact card with label, text, source attribution footer, "New ↻" button
- **Positive Affirmation** (result screen): full pull-quote layout with source badge, context card, footer buttons

### Feeling Out of Alignment — two-stage flow (on one page)
- **Stage 1:** Textarea visible. User types and submits. API called for `struggle` action.
- **Stage 1b:** After API response, energy center card renders below textarea. Footer shows "Different" + "Show me the way back".
- **Stage 2:** "Show me the way back" reveals the full response (reframe + exercise + clearing) — either as a second scrollable section on the same page, or a push to `/home/alignment/response`. Archon decides implementation — must feel like a distinct screen with a back button.

### Loading state
During API calls (on mount or on submit), show a shimmer skeleton. The `@keyframes shimmer` animation is already defined in globals.css. Show a shimmer rectangle approximately the size of the content that will appear.

## 3. Hypothesis
The current build skipped the navigation layer and used basic inline styles rather than following the mockup's design system. This is a frontend-only rebuild — the backend (generate route, system prompt, Supabase) is correct and does not need changes.

Archon must independently verify by reading:
1. `src/app/(app)/home/home-content.tsx` — current state management and UI
2. `src/app/globals.css` — available CSS variables and font definitions
3. `src/types/index.ts` — GenerateResponse, AlignmentResponse, UserConfig type shapes
4. `src/app/api/generate/route.ts` — response shape per action
5. `design/mockups/concept-final-hybrid.html` — the approved final mockup (source of truth for all visual decisions)

## 4. Recommended Resolution (IF Hypothesis is Proven)

### Files to create
- `src/app/(app)/home/affirmation/page.tsx`
- `src/app/(app)/home/daily-joe/page.tsx`
- `src/app/(app)/home/future-memory/page.tsx`
- `src/app/(app)/home/alignment/page.tsx`

### Files to modify
- `src/app/(app)/home/home-content.tsx` — rebuild home screen UI
- `src/app/(app)/home/page.tsx` — may need userConfig passed to new child pages (review)

### Home screen rebuild
Header:
- Left: time-of-day greeting sub-text (12px, `#B0A494`) + "Your Alignment" in Lora 22px
- Right: settings gear button (42x42, white, border-radius 13px)

Today's Blueprint card:
- White card, border-radius 24px, margin 18px 20px 0
- Label "TODAY'S BLUEPRINT" (10px, uppercase, letterspacing, `#7A8C5C`)
- Affirmation text in Lora 20px, line-height 1.56
- Footer with top border: source attribution (12px, `#C4B8A8`) left + "New ↻" (13px, `#9B8E7E`) right
- Auto-loads on mount

Actions label: "What do you need?" (11px uppercase, `#C4B8A8`, padding 20px 24px 12px)

Action tiles grid (2x2, gap 10px, padding 0 20px):
Each tile navigates via `router.push()` to its route.

### Affirmation result page
```
safe-top
header: ← back button | "Affirmation" title
flex:1 content:
  amber source badge: "✦ [exerciseType formatted]"
  pull quote: Lora 22px — content field
  context card (white, radius 16px):
    label: "From Florence Scovel Shinn"
    text: sourceAttribution field
result-footer:
  "Again ↻" outline btn → re-fetch
  "I receive this" filled btn → router.push('/home')
safe-bottom
```

### Daily Joe result page
```
safe-top
header: ← back | "Daily Dose of Joe"
flex:1 content:
  green source badge: "📖 Dr. Joe · [teaching field]"
  pull quote: Lora 22px — content field
  context card:
    label: "Why this matters for your work"
    text: whyItMatters field
    save hint: italic caption "Save this to your personal library from Settings →"
result-footer:
  "Save" outline btn (no-op for now)
  "I receive this" filled btn → router.push('/home')
safe-bottom
```

### Remember My Future result page
```
safe-top
header: ← back | "Remembering Your Future"
memory header card (dark green gradient):
  label: "FUTURE MEMORY"
  title: matched futureMemory label from userConfig (by memoryId) or "Your Future"
  sub: "Remembered · Already done"
memory body (flex:1):
  memory text: Lora 19px, line-height 1.65 — content field
  emotion tags row: emotionTags array as sage green pills
full-width filled btn: "I remember this" → router.push('/home')
safe-bottom
```

### Alignment page — Stage 1 (input)
```
safe-top
header: ← back | "Coming Back to Alignment"
body:
  "What's pulling you out?" — Lora 21px
  "Name it honestly. This is how we find the way back." — 14px #A89884
  auto-resize textarea (same pattern as onboarding)
  [on submit → show shimmer → show energy card]
  energy card (Signal style — see design system above):
    header: center badge + "Center N · Name — Subtitle"
    body: explanation text
footer (after energy card appears):
  "Different" outline btn → clear form + re-submit or clear result
  "Show me the way back" filled btn → show Stage 2
safe-bottom
```

### Alignment page — Stage 2 (response)
Implement as a second view within the same page (conditional render based on state), or as a nested route `/home/alignment/response` — Archon decides. Must have a back button pointing to Stage 1.
```
header: ← back (to stage 1) | "Your Way Back"
scrollable body:
  REFRAME section:
    white card: reframe text Lora 18px, source caption
  YOUR EXERCISE section:
    white card: amber FSS badge (exercise type) + instruction text (12px italic #A89884) + exercise text Lora 17px italic
  CENTER N CLEARING section:
    sage green gradient card: clearing title (13px bold #3D5C3D) + clearing text
full-width filled btn: "I'm back in alignment" → router.push('/home')
safe-bottom
```

## Constraints
- Do NOT change any API routes or system prompts
- Do NOT change Supabase schema
- Do NOT add new npm dependencies
- Use `var(--font-display)` and `var(--font-ui)` — do not import fonts directly
- Use existing CSS variables where they exist; inline styles acceptable for mockup-specific values not in variables
- All pages must be mobile-first, safe-area aware

## Validation
- `pnpm typecheck` — 0 errors
- `npx eslint src/` — 0 errors
- `pnpm test` — all existing tests pass (new UI pages may not have tests — that is acceptable)
- `pnpm build` — success
- Manual: tap each tile, confirm navigation and correct screen title
- Manual: complete alignment flow end to end
- Manual: Today's Blueprint loads on home mount
- Manual: Lora font renders on pull quotes
- Manual: emotion tags render on Remember My Future

## PR Description
Must follow the mandatory 7-section standard in CLAUDE.md. What Changed section must include every new file and every modified file.
