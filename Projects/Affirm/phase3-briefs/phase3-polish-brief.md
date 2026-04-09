---
project: Affirm
source: thane
imported: 2026-04-07
---
# Archon Diagnostic Brief — Phase 3 Polish: Emphasis Rendering, Content Structure, Home Blueprint Action

## 1. Problem Statement

After reviewing all screens against the approved mockup (`design/mockups/concept-final-hybrid.html`), five issues remain before Phase 3 is complete:

1. **Home Blueprint uses wrong content type** — calls `affirmation` (FSS exercise) when it should show a short declarative alignment statement (present-tense truth, not an exercise)
2. **No colored/italic emphasis rendering** — mockup uses italic sage green (`#5C7A5C`) on key phrases throughout all pull quotes; current build renders plain text
3. **Daily Dose of Joe content structure** — still too long; `whyItMatters` makes assumptive behavioral references; needs a two-part structure with a confirmatory follow-up
4. **Remember My Future** — one unbroken paragraph; needs paragraph breaks and emphasis for brevity and impact
5. **Bottom button safe area** — action buttons on all result screens sit too close to the bottom edge

## 2. Research Findings

### Issue 1 — Home Blueprint action
`src/app/(app)/home/home-content.tsx` calls `handleAction('affirmation')` on mount, which returns an FSS exercise (decree, erasure, etc.). The home card is designed to show a DECLARATION — a statement of present truth grounded in the user's config. Mockup example: *"The blueprint for abundant wealth already exists in the field. You are not waiting — you are receiving what is already yours."* This is declarative, not instructional.

Fix: Add a new `blueprint` action to `src/app/api/generate/route.ts` and `src/lib/ai/system-prompt.ts`. Home screen calls `blueprint`, not `affirmation`.

### Issue 2 — Emphasis rendering
The mockup HTML uses `<em>` tags inline within content strings, rendered in CSS as `color: #5C7A5C; font-style: italic`. Example from mockup:
```
"The blueprint for <em>abundant wealth</em> already exists in the field."
"When you can <em>feel the emotion of your future</em> before it happens"
"<em>The certainty — not surprise, but recognition.</em> Some deeper part..."
```

Current system prompt does not instruct Claude to use `<em>` tags, and current UI components render content as plain text strings. Neither the prompt nor the UI handles emphasis.

Fix: Two-part —
- **Prompt**: Instruct Claude to wrap 1-3 key phrases per content field in `<em>` tags (not every sentence — selective, high-impact emphasis only)
- **UI**: Create a shared `renderEmphasis(text: string)` utility that splits on `<em>` tags and renders spans with `{ color: '#5C7A5C', fontStyle: 'italic' }` — use this function wherever content is displayed in pull-quote context

The `renderEmphasis` utility should also handle `\n\n` as paragraph breaks (render a `<br/><br/>` or a margin-top paragraph element).

### Issue 3 — Daily Joe structure
Current `content` field is a single block of text too long. Current `whyItMatters` references specific user behaviors ("12-hour days") which is assumptive. 

Fix in `system-prompt.ts` output spec for `daily-joe`:
- `content`: Two parts separated by `\n\n`:
  - Part 1: The core Joe teaching (1-3 sentences, ends with the insight)
  - Part 2: A short confirmatory statement or question (1-2 sentences) that references a specific elevated emotion or feeling from the user's config — not behavioral analysis. Example: "Do you remember the feeling of [elevated emotion from config]? That feeling IS the signal."
- `whyItMatters`: 1-2 sentences ONLY. Confirmatory and uplifting. Must NOT reference the user's specific behaviors, habits, or current struggles. Only references the connection between the teaching and their future state.

### Issue 4 — Remember My Future content
Current content is one long paragraph (5-8 sentences unbroken). Mockup shows 2-3 SHORT paragraphs separated by line breaks with `<em>` emphasis on key emotional moments.

Fix in `system-prompt.ts` output spec for `future-memory`:
- `content`: 3-4 sentences MAXIMUM, broken into 2-3 short paragraphs separated by `\n\n`. Each paragraph is one distinct sensory or emotional moment. Use `<em>` tags on the single most powerful phrase per paragraph. Use "I remember..." framing.

### Issue 5 — Bottom button spacing
All result page components (`affirmation-content.tsx`, `daily-joe-content.tsx`, `future-memory-content.tsx`, `alignment-content.tsx`) have footer padding of `16px 20px` and safe-area bottom of `env(safe-area-inset-bottom, 34px)`. On iPhone, the button appears too close to the home indicator. 

Fix: Increase the `safe-bottom` spacer from `env(safe-area-inset-bottom, 34px)` to `env(safe-area-inset-bottom, 48px)` on all result screens, and add `paddingBottom: 8px` to the footer container.

## 3. Hypothesis

All five issues are independently fixable:
1. New `blueprint` action — backend + home UI change
2. `renderEmphasis` utility + prompt instruction — shared utility + prompt change + UI updates
3. Daily Joe prompt restructure — prompt only
4. Future Memory prompt restructure — prompt only
5. Bottom spacing — UI only

Archon must independently verify by reading:
1. `src/app/api/generate/route.ts` — current valid actions list and response handling
2. `src/lib/ai/system-prompt.ts` — current output format specs for all actions
3. `src/app/(app)/home/home-content.tsx` — how blueprint action is called and rendered
4. `src/app/(app)/home/affirmation/affirmation-content.tsx` — how content is rendered (reference for emphasis pattern)
5. `src/app/(app)/home/daily-joe/daily-joe-content.tsx` — how content and whyItMatters are rendered
6. `src/app/(app)/home/future-memory/future-memory-content.tsx` — how content and emotionTags are rendered
7. `src/app/(app)/home/alignment/alignment-content.tsx` — how reframe and exercise text are rendered
8. `design/mockups/concept-final-hybrid.html` — the visual reference (CSS for `em` tag: look for `.aff-text em`, `.result-pull-quote em`, `.reframe-text em`, `.memory-text strong`)

## 4. Recommended Resolution (IF Hypothesis is Proven)

### Step 1 — Add `blueprint` action to generate route
In `src/app/api/generate/route.ts`:
- Add `'blueprint'` to `VALID_ACTIONS` array
- Add case in `buildActionPrompt`: `case 'blueprint': return 'Generate a Today\'s Blueprint alignment statement for my home screen.'`

In `src/lib/ai/system-prompt.ts`, add output spec:
```
For action "blueprint":
{ "content": "string — a SHORT declarative alignment statement. 2-3 sentences of present-tense truth grounded in this user's config. This is NOT an exercise. It is a statement of what IS — the reality they are stepping into. Use <em> tags on 1-2 key phrases. NO exercise instructions, NO 'speak these words', NO preamble. Example style: 'The blueprint for <em>abundant wealth</em> already exists in the field. You are not waiting — you are receiving what is already yours.'", "exerciseType": "decree", "sourceAttribution": "string — 1 sentence, e.g. 'FSS · The Decree'" }
```

In `src/app/(app)/home/home-content.tsx`: Change `handleAction('affirmation')` and `fetchBlueprint` to use `action: 'blueprint'`.

### Step 2 — Create `renderEmphasis` shared utility
Create `src/lib/render-emphasis.tsx`:
```tsx
import React from 'react'

export function renderEmphasis(text: string, emphasisStyle?: React.CSSProperties): React.ReactNode {
  const defaultEmphasisStyle: React.CSSProperties = {
    color: '#5C7A5C',
    fontStyle: 'italic',
    ...emphasisStyle,
  }
  
  // Split on paragraph breaks first
  const paragraphs = text.split(/\n\n/)
  
  return (
    <>
      {paragraphs.map((para, pIdx) => {
        // Split on <em> tags
        const parts = para.split(/(<em>.*?<\/em>)/gs)
        const rendered = parts.map((part, i) => {
          if (part.startsWith('<em>') && part.endsWith('</em>')) {
            const inner = part.slice(4, -5)
            return <span key={i} style={defaultEmphasisStyle}>{inner}</span>
          }
          return <span key={i}>{part}</span>
        })
        return (
          <React.Fragment key={pIdx}>
            {pIdx > 0 && <br />}
            {pIdx > 0 && <br />}
            {rendered}
          </React.Fragment>
        )
      })}
    </>
  )
}
```

Apply `renderEmphasis()` in place of plain text rendering for:
- Home blueprint card: `blueprintData.content`
- Affirmation pull quote: `result.content`
- Daily Joe pull quote: `result.content`
- Future Memory text: `result.content`
- Alignment reframe text: `result.reframe.text`
- Alignment exercise text: `result.exercise.text`

### Step 3 — Update Daily Joe output spec
Replace in `system-prompt.ts`:
```
For action "daily-joe":
{
  "content": "string — TWO parts separated by \\n\\n. Part 1: ONE Joe Dispenza teaching or insight, 1-3 sentences, ending with the sharpest point of the insight (e.g. 'Matter follows.'). Part 2: 1-2 sentences — a short confirmatory statement or question that references a specific elevated emotion or future vision from this user's config. NOT behavioral analysis. Example part 2: 'Do you remember the feeling of freedom — already complete, already whole? That feeling is the signal the field is waiting for.' Use <em> tags on 1-2 key phrases across the full content.",
  "teaching": "string — one phrase, the name of the teaching (e.g. 'The Electromagnetic Signature'). No sentence.",
  "whyItMatters": "string — 1-2 sentences ONLY. Confirmatory and uplifting. Connects this teaching to the user's FUTURE state, not their current struggles or behaviors. No assumptions about what the user is doing wrong."
}
```

### Step 4 — Update Future Memory output spec
Replace in `system-prompt.ts`:
```
For action "future-memory":
{
  "content": "string — MAXIMUM 3-4 sentences, broken into 2-3 short paragraphs with \\n\\n between them. Each paragraph is one distinct sensory or emotional moment, written in first person past tense ('I remember...'). Use <em> on the single most powerful phrase in one of the paragraphs. Extreme brevity. Each paragraph should be 1-2 sentences only. Total length should fit comfortably on a phone screen without scrolling.",
  "memoryId": "string",
  "emotionTags": ["string"]
}
```

### Step 5 — Fix bottom button spacing
In all result screen components (`affirmation-content.tsx`, `daily-joe-content.tsx`, `future-memory-content.tsx`, `alignment-content.tsx`):
- Change the safe-bottom div from `height: 'env(safe-area-inset-bottom, 34px)'` to `height: 'env(safe-area-inset-bottom, 56px)'`
- Add `paddingBottom: 8` to the footer container div

### What NOT to change
- `struggle` action spec — working correctly
- `future-memory` emotionTags rendering — working correctly
- Alignment flow stage logic — working correctly
- All routing, back buttons, screen titles — working correctly
- `src/app/api/onboarding/` — do not touch

## Validation
- `pnpm typecheck` — 0 errors
- `npx eslint src/` — 0 errors
- `pnpm test` — all tests pass (renderEmphasis utility should have at least basic tests)
- `pnpm build` — success
- Manual: Home card shows a 2-3 sentence declarative statement with colored italic emphasis on key phrase
- Manual: Positive Affirmation shows emphasis in sage green italic on key phrase
- Manual: Daily Joe shows two-part content with paragraph break, confirmatory follow-up
- Manual: Remember My Future shows 2-3 short paragraphs with emphasis, NO long blob
- Manual: Alignment reframe shows paragraph breaks and emphasis
- Manual: Bottom buttons on all screens have adequate spacing from screen edge

## PR Description
Must follow the mandatory 7-section standard in CLAUDE.md. What Changed section must list every file touched.
