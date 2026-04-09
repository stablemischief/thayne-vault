---
project: Affirm
source: thane
imported: 2026-04-07
---
# Archon Diagnostic Brief — Fix Prompt Verbosity: Affirmation and Daily Joe Actions

## 1. Problem Statement

The `affirmation` and `daily-joe` actions are generating excessively long responses that break the UI. The "Today's Blueprint" home card and the Positive Affirmation result screen both show multi-paragraph text including instruction preambles ("Speak these words out loud, slowly — 3 times...") which should never appear in the `content` field. The Daily Dose of Joe screen renders what looks like a chapter excerpt instead of a single punchy teaching. The UI is designed for short, elegant content — the prompts are not constraining Claude to produce it.

The `future-memory` and `struggle` actions are working correctly — do NOT touch them.

## 2. Research Findings

**File to fix:** `src/lib/ai/system-prompt.ts` — specifically the OUTPUT FORMAT INSTRUCTIONS section.

**Current output spec for `affirmation`:**
```
{ "content": "string — the full exercise text", "exerciseType": "...", "sourceAttribution": "string — e.g. Florence Scovel Shinn · The Decree" }
```
"the full exercise text" causes Claude to include instruction preambles, spoken-word directions, and multi-paragraph content. The UI renders `content` as the main pull-quote — it must be short.

**Current output spec for `daily-joe`:**
```
{ "content": "string — the teaching connected to this user's work", "teaching": "string — the teaching name/concept", "whyItMatters": "string — why this matters for THIS user specifically" }
```
No length constraint — Claude writes paragraphs. The UI displays `content` as a Lora pull-quote at 22px. It needs to be a single Joe quote or insight, not a dissertation.

**Current `sourceAttribution`:**
Spec says "e.g. Florence Scovel Shinn · The Decree" — Claude sometimes generates a full explanatory paragraph here instead of a brief attribution string. The UI context card is designed to show 1-2 sentences of explanation, not a raw label string.

**Mockup reference (`design/mockups/concept-final-hybrid.html`):**
- Affirmation pull-quote: "I give infinite thanks that right abundance is mine now — flowing freely, in expected and unexpected ways, with ease and grace." — ONE sentence.
- Daily Joe pull-quote: "When you can feel the emotion of your future before it happens, you stop looking for it. How could you look for what you already feel?" — TWO sentences.
- Context card ("Why this matters"): "Your blueprint for abundance is already in the field. The shift isn't trying harder — it's feeling the reality of it now, before the outer world confirms it." — TWO sentences.

**The instruction preamble** ("Speak these words out loud, slowly — 3 times. With each repetition...") is already hardcoded in the `struggle` action's `exercise.instruction` field. For the `affirmation` action, this instruction should NOT appear in `content` at all. It is shown contextually in the UI if needed, but NOT as part of the main pull-quote content.

## 3. Hypothesis

The output format spec strings are too permissive. Claude fills the space it's given. Adding explicit length constraints and clarifying what belongs in each field will produce tight, elegant content that matches the UI design intent.

Archon must independently verify by reading:
1. `src/lib/ai/system-prompt.ts` — full file, especially OUTPUT FORMAT INSTRUCTIONS
2. `src/app/(app)/home/affirmation/affirmation-content.tsx` — how `content` and `sourceAttribution` are rendered
3. `src/app/(app)/home/daily-joe/daily-joe-content.tsx` — how `content`, `teaching`, and `whyItMatters` are rendered
4. `design/mockups/concept-final-hybrid.html` — the visual reference for intended content length and tone

## 4. Recommended Resolution (IF Hypothesis is Proven)

Update ONLY the OUTPUT FORMAT INSTRUCTIONS section of `src/lib/ai/system-prompt.ts`. No other file changes.

### Affirmation action spec — replace with:
```
For action "affirmation":
{
  "content": "string — the core affirmation, decree, or exercise words ONLY. 1-3 sentences maximum. NO instruction preamble (no 'Speak these words...', no 'Say this 3 times', no meta-commentary). This is the pull-quote the user reads and feels. Make it personal, grounded, and powerful.",
  "exerciseType": "decree|erasure|future-memory|emotion-anchor|blessing|gratitude|claim",
  "sourceAttribution": "string — 1-2 sentences max. A brief note on why this specific exercise type fits this user's current work. NOT just a label. Example: 'A gratitude treatment grounds the signal you are already sending to the field — abundance is yours now, not later.'"
}
```

### Daily Joe action spec — replace with:
```
For action "daily-joe":
{
  "content": "string — ONE Joe Dispenza insight or teaching, expressed as a pull-quote. 2-4 sentences maximum. This is Joe's framework distilled to its sharpest point — the thing that stops you in your tracks. No preamble, no setup, no summary. Start with the insight itself.",
  "teaching": "string — the name of the teaching or concept (e.g. 'The Electromagnetic Signature', 'Becoming Supernatural'). One phrase, no sentence.",
  "whyItMatters": "string — 1-2 sentences connecting this specific teaching to THIS user's current work and config. Personal. Direct. No generic wellness language."
}
```

### Do NOT change:
- `future-memory` action spec — working correctly
- `struggle` action spec — working correctly
- Any other part of the system prompt
- Any API routes, UI files, or other code

## Validation
- `pnpm typecheck` — 0 errors
- `npx eslint src/` — 0 errors
- `pnpm test` — all tests pass
- `pnpm build` — success
- Manual test: tap "Positive Affirmation" tile — content must be 1-3 sentences, no instruction preamble visible in the pull-quote
- Manual test: tap "Daily Dose of Joe" tile — pull-quote must be 2-4 sentences of Joe's distilled insight, `whyItMatters` must be 1-2 sentences
- Manual test: home screen "Today's Blueprint" — short and elegant, reads like a decree not an exercise manual
- Manual test: "Remember My Future" and "Feeling Out of Alignment" — verify these still work correctly and are unchanged

## PR Description
Must follow the mandatory 7-section standard in CLAUDE.md. This is a small PR — 1 file, targeted string changes.
