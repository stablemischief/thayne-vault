---
project: Affirm
source: thane
imported: 2026-04-07
---
# Archon Diagnostic Brief — Phase 3 UX: Blueprint Caching, Daily Joe Restructure, Prefetch Latency Fix

## 1. Problem Statement

Three UX issues remain after PR #16:

1. **Blueprint regenerates on every home screen visit** — should only generate on: (a) new calendar date, or (b) user explicitly clicks a refresh button
2. **Daily Dose of Joe content structure** — still too analytical and assumptive; should be a Joe quote/teaching → relatable connecting statement → thought-provoking question
3. **8-10 second latency on every action screen** — user taps a tile and waits with nothing happening; kills the positive momentum the app is designed to create

## 2. Research Findings

### Issue 1 — Blueprint caching
`src/app/(app)/home/home-content.tsx` calls `fetchBlueprint()` inside a `useEffect` on every mount. There is no caching. Every home screen visit (including back-navigation from result screens) triggers a new Claude API call.

The home blueprint card (as shown in design/mockups/concept-final-hybrid.html) has a small ↻ refresh icon in the top-right corner of the card frame. This is the only user-controlled refresh trigger.

Fix: 
- On `fetchBlueprint()` success, store the result in `localStorage` under key `affirm_blueprint_{YYYY-MM-DD}` (today's date in user's timezone)
- On mount, check localStorage for today's key. If found, use it (no API call). If not found (new day or first visit), call the API.
- Add a small ↻ refresh button to the top-right corner of the blueprint card. On click: clear the localStorage key, call `fetchBlueprint()` again.
- Clean up keys from previous days (delete any `affirm_blueprint_*` key that doesn't match today).

### Issue 2 — Daily Joe prompt structure
The current `daily-joe` content prompt still produces behavioral analysis and assumptive references (e.g., "your 12-hour days", "your 6am Body Electric meditation"). The target structure from the approved mockup is:

**Part 1** — A Joe Dispenza teaching or insight. This can be:
  - A quote from one of his books, talks, or courses (in his framework/language)
  - A core principle from his work
  - It MAY loosely relate to the user's stated focus areas, or may simply be a great standalone teaching
  - 1-3 sentences. Sharp. Direct. Ends on the insight.

**Part 2** — A relatable connecting statement (1-2 sentences). This bridges the teaching to the user's inner experience — NOT their behaviors, schedule, or specific actions. Example: "You already know this at some level. The question is whether you're living from that knowing."

**Part 3** — A thought-provoking question (1 sentence). Simple. Invites the user to personally connect with the teaching. Example: "How could you look for what you already feel?"

The three parts are separated by `\n\n` within the `content` field.

The `whyItMatters` card: 1-2 sentences maximum. Brief, uplifting. Connects the teaching to the user's future state (not current struggles). This is a CONTEXT CARD, not an analysis. Example: "Your blueprint for abundance is already in the field. The shift isn't trying harder — it's feeling the reality of it now, before the outer world confirms it."

Update the `daily-joe` output spec in `src/lib/ai/system-prompt.ts` to enforce this structure explicitly.

### Issue 3 — Latency via prefetch
Every result screen makes a fresh Claude API call on mount, taking 8-10 seconds. The fix is to prefetch all actions in the background when the home screen loads, so tapping a tile shows content instantly.

**Architecture:**
- After the home screen mounts and blueprint is resolved (or loaded from cache), kick off background prefetch for all 4 actions: `affirmation`, `daily-joe`, `future-memory`, `struggle`
- Store each result in `sessionStorage` under keys: `affirm_prefetch_affirmation`, `affirm_prefetch_daily-joe`, `affirm_prefetch_future-memory`, `affirm_prefetch_struggle`
- Each result screen (`affirmation-content.tsx`, `daily-joe-content.tsx`, `future-memory-content.tsx`, `alignment-content.tsx`) checks `sessionStorage` on mount. If a cached result exists: render immediately (no loading state). Then clear the key from sessionStorage so the next visit gets a fresh fetch.
- If sessionStorage is empty (user navigated directly, or cache was cleared): fall back to the existing API fetch with shimmer loading state.
- The `future-memory` prefetch needs the `futureMemoryId` — read this from the `userConfig` prop that is passed to `home-content.tsx` and include it in the prefetch request body.

**Important:** Prefetch calls should be fire-and-forget (no await, no error blocking). If a prefetch fails silently, the result screen falls back gracefully to its own fetch. Do not block home screen rendering on prefetch completion.

**SessionStorage is correct here** (not localStorage): prefetched content is session-scoped and should not persist across browser sessions — each visit should get fresh content.

Archon must independently verify by reading:
1. `src/app/(app)/home/home-content.tsx` — current blueprint fetch and tile navigation
2. `src/app/(app)/home/daily-joe/daily-joe-content.tsx` — how content field is consumed
3. `src/app/(app)/home/affirmation/affirmation-content.tsx` — pattern to replicate for prefetch consumption
4. `src/app/(app)/home/future-memory/future-memory-content.tsx` — note it receives `userConfig` via page.tsx
5. `src/app/(app)/home/alignment/alignment-content.tsx` — the struggle action, note it has a two-stage flow (submit struggle first)
6. `src/lib/ai/system-prompt.ts` — current daily-joe output spec
7. `design/mockups/concept-final-hybrid.html` — visual reference for blueprint card refresh button placement

## 3. Hypothesis

All three issues are independently fixable:
1. localStorage + date key + refresh button → blueprint stability
2. Prompt restructure → Daily Joe format correction
3. sessionStorage prefetch → near-zero perceived latency for affirmation, daily-joe, future-memory

Note on `struggle`/alignment: This action requires user input (what they're struggling with) before generating content, so it CANNOT be prefetched. The alignment screen will still have latency. That is acceptable — the user is entering context before generating, so the wait feels intentional.

## 4. Recommended Resolution

### Fix 1 — Blueprint localStorage caching

In `src/app/(app)/home/home-content.tsx`:

```typescript
function getTodayKey() {
  const d = new Date()
  return `affirm_blueprint_${d.getFullYear()}-${String(d.getMonth()+1).padStart(2,'0')}-${String(d.getDate()).padStart(2,'0')}`
}

function loadCachedBlueprint(): GenerateResponse | null {
  try {
    const todayKey = getTodayKey()
    // Clean up old keys
    Object.keys(localStorage).forEach(k => {
      if (k.startsWith('affirm_blueprint_') && k !== todayKey) localStorage.removeItem(k)
    })
    const raw = localStorage.getItem(todayKey)
    return raw ? JSON.parse(raw) : null
  } catch { return null }
}

function saveBlueprintCache(data: GenerateResponse) {
  try { localStorage.setItem(getTodayKey(), JSON.stringify(data)) } catch {}
}
```

In `fetchBlueprint()`: Before API call, check `loadCachedBlueprint()`. If found, set state and return early. After successful API call, call `saveBlueprintCache(data)`.

Add refresh button to the blueprint card UI — small ↻ icon button in the top-right corner of the card. Style: 28px circle, background transparent, border none, color `#B0A494`, fontSize 14. On click: `localStorage.removeItem(getTodayKey())` then `fetchBlueprint()`.

### Fix 2 — Daily Joe prompt

Replace the `daily-joe` output spec in `src/lib/ai/system-prompt.ts`:

```
For action "daily-joe":
{
  "content": "string — THREE parts separated by \\n\\n. Part 1: A Joe Dispenza teaching or insight — a real principle from his framework (books, talks, courses). 1-3 sentences. Sharp and direct. May loosely relate to this user's focus areas or may stand alone as a great teaching. Part 2: A relatable connecting statement (1-2 sentences). Bridges the teaching to the user's inner experience — NOT their behaviors, schedule, or specific actions. Part 3: ONE thought-provoking question (1 sentence) that invites the user to personally connect with the teaching. Use <em> on 1-2 key phrases across all three parts.",
  "teaching": "string — the name of the teaching or concept. One phrase only (e.g. 'The Electromagnetic Signature', 'Becoming Supernatural').",
  "whyItMatters": "string — 1-2 sentences ONLY. A brief context note connecting this teaching to the user's future state. Uplifting, not analytical. No behavioral references. No assumptions about what the user is struggling with."
}
```

### Fix 3 — Prefetch in home-content.tsx

After blueprint resolves in `home-content.tsx`, fire prefetch for non-struggle actions:

```typescript
function prefetchAction(action: string, body?: object) {
  const key = `affirm_prefetch_${action}`
  try { sessionStorage.removeItem(key) } catch {}
  
  fetch('/api/generate', {
    method: 'POST',
    headers: { 'Content-Type': 'application/json' },
    body: JSON.stringify({ action, ...body }),
  })
    .then(r => r.ok ? r.json() : null)
    .then(data => {
      if (data) {
        try { sessionStorage.setItem(key, JSON.stringify(data)) } catch {}
      }
    })
    .catch(() => {}) // silent fail
}

// Call after blueprint resolved (in fetchBlueprint finally block or after cache hit):
prefetchAction('affirmation')
prefetchAction('daily-joe')
prefetchAction('future-memory', { futureMemoryId: userConfig.futureMemories?.[0]?.id })
// Do NOT prefetch 'struggle' — requires user input
```

In each result screen, add cache check at the top of the fetch function:
```typescript
// Check prefetch cache first
try {
  const cached = sessionStorage.getItem('affirm_prefetch_affirmation') // (or daily-joe, future-memory)
  if (cached) {
    sessionStorage.removeItem('affirm_prefetch_affirmation')
    setResult(JSON.parse(cached))
    setLoading(false)
    return
  }
} catch {}
// Fall through to normal fetch
```

### What NOT to change
- `struggle`/alignment action — cannot prefetch (requires user input), leave as-is
- `renderEmphasis` utility — working correctly
- `blueprint` action prompt — working correctly
- All routing, back buttons, screen titles — working correctly
- Test count: tests should increase or stay same, not decrease

## Validation
- `pnpm typecheck` — 0 errors
- `npx eslint src/` — 0 errors
- `pnpm test` — all tests pass
- `pnpm build` — success
- Manual: Navigate home → blueprint card shows. Navigate away and back → same content (no reload). At midnight: new content generates.
- Manual: Click ↻ on blueprint card → new blueprint generates
- Manual: Tap "Daily Dose of Joe" → content arrives INSTANTLY (from prefetch cache), structured as quote → connecting statement → question
- Manual: Tap "Positive Affirmation" → instant (from cache)
- Manual: Tap "Remember My Future" → instant (from cache)
- Manual: Tap "Feeling Out of Alignment" → still has load time (expected, requires input)
- Manual: Daily Joe content has 3 clear paragraphs, no behavioral assumptions, whyItMatters is brief

## PR Description
Must follow the mandatory 7-section standard in CLAUDE.md. List every changed file.
