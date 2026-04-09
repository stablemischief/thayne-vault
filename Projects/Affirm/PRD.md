# Product Requirements Document: Affirm

## Executive Summary

**Affirm** is a personal alignment tool that helps users shift their mental and emotional state into coherence with the blueprint they are creating — grounded in the teachings of Dr. Joe Dispenza and Florence Scovel Shinn. It is designed for individuals actively doing inner work who need an on-demand tool to interrupt negative thought loops, return to elevated emotion, and remember their future. 

**MVP Goal:** A working PWA (iOS-first) where a single user can complete an onboarding session, build a personal config, and use four core action buttons to receive personalized, AI-generated alignment content grounded in their config and the Dispenza/FSS framework.

---

## Mission

Most people doing inner work have a powerful practice during meditation — and then default back to old patterns the moment they pick up their phone. Affirm is the bridge: a tool that meets the user in that low moment and uses their own blueprint, their own future memories, and their own elevated emotion anchors to pull them back into alignment.

This is not a generic affirmation app. Every response is grounded in the user's personal config and delivered in the language and framework of Dr. Joe Dispenza and Florence Scovel Shinn.

### Core Principles

1. **Personal over generic** — Every piece of content is generated from the user's config. Nothing is pulled from a static list.
2. **State shift over information** — The goal of every interaction is an emotional/energetic shift, not intellectual content delivery.
3. **Framework integrity** — All content must be grounded in Dispenza's neuroscience/quantum field framework and/or FSS's decree/affirmation framework. No generic wellness-speak.
4. **Remember, don't want** — Language always reflects the "already done" / "remembering the future" principle. Never "I want" or "I hope" — always "I remember," "I receive," "It is done."

---

## Target Users

**Individuals actively practicing Dr. Joe Dispenza's work** who:
- Are doing daily meditation and inner work
- Struggle with negative thought loops or low-frequency states during the day
- Want a tool that speaks their specific language and knows their specific work
- May eventually share this with a small community of like-minded practitioners

**Technical Comfort Level:** Non-technical (must be dead simple UX)

---

## MVP Scope

### In Scope

**Onboarding / Config:**
- ✅ Conversational "Get to Know Me" onboarding session (AI-driven, one question at a time, target 10-12 questions, hard max 20)
- ✅ Onboarding builds a personal config covering: active work areas, limiting blueprint patterns being rewired, future memories from meditation, elevated emotion anchors, energy center focus areas
- ✅ Config stored per user in Supabase
- ✅ Settings page to view and edit config after onboarding

**Home Screen:**
- ✅ Opens with a personalized affirmation card (AI-generated from config) with source attribution and refresh button
- ✅ No standalone check-in strip — entry to alignment flow is via action tile only
- ✅ 2×2 action tile grid — four distinct actions, no redundancy

**Action Buttons (final labels):**
- ✅ **Positive Affirmation** — Fresh personalized affirmation + FSS exercise type + context card
- ✅ **Daily Dose of Joe** — Key teaching from Joe's framework relevant to user's config + saveable to personal library
- ✅ **Remember My Future** — First-person past-tense sensory "memory" of a future vision from config, written as already experienced. Never future tense.
- ✅ **Feeling Out of Alignment** — Opens alignment flow: text input → Signal-style energy center diagnostic card → full response screen (reframe + exercise + clearing practice)

**AI / Content Engine:**
- ✅ All content generated dynamically by Claude API — no static lists
- ✅ System prompt encodes full Dispenza framework (energy centers, breath technique, brain states, blueprint/field model, elevated emotions, "remembering the future" principle)
- ✅ System prompt encodes FSS Affirmation Exercise Framework (7 exercise types: Decree, Erasure+Replacement, Future Memory Recall, Elevated Emotion Anchor, Blessing, Gratitude Treatment, The Claim)
- ✅ User config injected into every generation call
- ✅ Energy Center Diagnostic: AI identifies which center(s) are implicated in the user's struggle and delivers a targeted clearing practice

**Auth / Multi-tenancy:**
- ✅ User accounts (Supabase Auth)
- ✅ Each user has isolated config — no shared data
- ✅ Invite-only access in v1 (no public sign-up)
- ✅ Admin can invite users

**Platform:**
- ✅ PWA — iOS-first (installs cleanly on iPhone/iPad home screen)
- ✅ Responsive for iPad

### Out of Scope (v2+)

- ❌ Push notifications / proactive outreach
- ❌ Audio / guided meditation integration
- ❌ Community features (shared affirmations, group feed)
- ❌ Streak tracking / gamification
- ❌ Apple Watch / widget support
- ❌ React Native conversion (evaluated post-MVP)
- ❌ Android support
- ❌ Social login (Google/Apple) — email auth only for v1

---

## Knowledge Base (AI System Prompt Sources)

The AI content engine must be trained on the following frameworks. These are encoded in the system prompt — not user-configurable.

### Dr. Joe Dispenza Framework

**Core model:**
- Personality creates personal reality (thoughts + actions + feelings)
- 95% of behavior is subconscious/conditioned by mid-life
- Blueprint exists in the field as frequency/light; matter must follow when blueprint changes
- Energy centers 1-3 (survival/lower frequency) vs 4-8 (creative/higher frequency)
- Breath moves energy from lower centers to brain/pineal, activating the field connection
- "Nobody, no one, no thing, nowhere, in no time" — the state of pure consciousness / quantum field access
- Heart-brain coherence = the Wi-Fi signal to the field
- Thought = electrical charge in the field; feeling = magnetic charge; together they create electromagnetic signature

**Elevated emotions:** Joy, Gratitude, Love, Freedom, Inspiration, Awe, Wholeness, Peace

**"Remembering the Future" principle:**
- Living in lack = thinking about what you don't yet have → signals separation to the field
- Remembering the future = treating the meditated vision as an already-experienced memory → signals completion to the field
- Language must always reflect the "already done" reality

**Energy Center Diagnostic Map:**
| Center | Location | Implicated when user experiences... |
|--------|----------|--------------------------------------|
| 1 | Root | Survival fear, financial insecurity, feeling unsafe |
| 2 | Sacral | Guilt, shame, unworthiness, victim mentality |
| 3 | Solar Plexus | Anger, frustration, need to control, competition |
| 4 | Heart | Grief, disconnection, inability to give/receive love |
| 5 | Throat | Suppressed truth, inauthenticity, unexpressed feelings |
| 6 | Third Eye/Pineal | Mental fog, disconnected from intuition, can't access the field |
| 7 | Crown | Separation from the divine, purposelessness |
| 8 | Field | Disconnection from the quantum field, inability to feel beyond the body |

**Meditation catalog awareness (for "Daily Dose of Joe" and guidance):**
- Generating Series (15-min quick state shift)
- Morning/Evening Meditations (daily anchors)
- Changing Boxes (stepping into a reality where the problem doesn't exist)
- Tuning Into New Potentials (manifestation, emotional rehearsal)
- Changing Beliefs and Perceptions (stubborn limiting beliefs)
- The Generous Present Moment (dissolving into the Eternal Now)
- Blessing of the Energy Centers / BOTEC (Levels 1-12, center-by-center coherence)
- Synchronize Your Energy Series (Abundance, Health, Love, Mystical, New Life — user config determines relevance)
- Pineal Gland Meditation (advanced, Electric Universe framework)

### Florence Scovel Shinn Framework

**Core model:**
- Words and thoughts have creative power — they impress the subconscious and the field
- Affirmations are decrees, not wishes — spoken as already true
- The old record must be erased before the new can take hold (parallel to Joe's blueprint work)
- Gratitude is "the great multiplier"
- Making your claim = stating with authority what is yours by divine right

**Affirmation Exercise Framework (7 types — AI selects and constructs dynamically):**

1. **The Decree** — Bold spoken declaration that the thing is already so. Used for doubt/lack-thinking.
   - *"Infinite Intelligence, I decree that my right abundance flows to me now in expected and unexpected ways."*

2. **The Erasure + Replacement** — Names the old record, speaks the new one over it. Used when a specific limiting belief is active.
   - *"I release the belief that I must struggle for abundance. I now accept that abundance is my divine birthright and flows to me easily."*

3. **The Future Memory Recall** — First-person sensory micro-visualization written as a past memory of the desired future. Used to drop into the "already done" feeling in 30 seconds.

4. **The Elevated Emotion Anchor** — Walks user from current state into a specific elevated emotion using their personal context as the bridge.

5. **The Blessing** — A brief blessing spoken over the area of struggle — treating it not as a problem but as an energy center to bring into coherence.

6. **The Gratitude Treatment** — Specific gratitude for what is already yours in the field — thanking in advance, as if received.

7. **The Claim** — Short, declarative, personal. States what is yours by divine right. Quick state-shifter.

---

## User Stories

### Primary User Stories

1. **As a user, I want to complete a "Get to Know Me" onboarding session**, so that the app understands my specific inner work and can personalize all content to my blueprint.
   - Example: "The AI asks me one question at a time about what I'm working on — limiting beliefs, future visions, emotional anchors — and builds my config from my answers."

2. **As a user, I want to open the app and immediately receive an aligned affirmation**, so that every time I pick up my phone, I'm pulled toward my blueprint rather than away from it.

3. **As a user, I want to tap "I'm Struggling" and describe what's pulling me out of alignment**, so that the AI can identify which energy center is implicated, offer a targeted clearing practice, and deliver a Joe/FSS-grounded reframe.

4. **As a user, I want to tap "Remember My Future" and be placed inside a sensory memory of my desired future**, so that I can feel the "already done" state in 30 seconds or less.

5. **As a user, I want to edit my config in Settings**, so that as my work evolves, the app evolves with me.

6. **As an admin, I want to invite other users**, so that I can share access with my community without opening public sign-up.

### Technical User Stories

7. **As a developer, I want each user's config to be fully isolated in Supabase**, so that no user's personal data is visible to or affects any other user.

8. **As a developer, I want the AI generation layer to accept a user config object and framework system prompt**, so that all content is personalized and framework-grounded without hardcoding any user-specific data into the codebase.

9. **As a developer, I want the PWA to install cleanly on iOS home screens**, so that the experience feels native and the app is a single tap away.

---

## Core Architecture & Patterns

### Architecture: Feature-based (Next.js App Router)

```
affirm/
├── app/
│   ├── (auth)/
│   │   ├── login/
│   │   └── invite/[token]/
│   ├── (app)/
│   │   ├── home/              # Main screen: affirmation + check-in + 4 buttons
│   │   ├── onboarding/        # Conversational config builder
│   │   ├── settings/          # Config editor
│   │   └── struggle/          # I'm Struggling flow
│   └── api/
│       ├── generate/          # AI generation endpoint
│       ├── config/            # CRUD for user config
│       └── invite/            # Admin invite flow
├── lib/
│   ├── ai/
│   │   ├── system-prompt.ts   # Dispenza + FSS framework encoding
│   │   ├── generate.ts        # Claude API wrapper
│   │   └── exercise-engine.ts # Exercise type selection logic
│   ├── supabase/
│   └── config/
│       └── schema.ts          # User config shape/types
├── components/
│   ├── ActionButton.tsx
│   ├── AffirmationCard.tsx
│   ├── CheckIn.tsx
│   ├── EnergyCenter.tsx
│   └── ExerciseDisplay.tsx
└── public/
    └── manifest.json          # PWA manifest
```

### Key Patterns

**1. Config-driven AI generation**
Every AI call receives: (a) the full framework system prompt, (b) the user's config object. No user-specific data is hardcoded in prompts. Config is fetched server-side per request.

**2. Exercise Engine**
`exercise-engine.ts` contains the logic for selecting which FSS exercise type(s) to deploy based on the user's described struggle and energy center diagnostic. The AI then *constructs* the exercise using this type as a template — it does not pull from a static list.

**3. Energy Center Diagnostic**
The AI struggle response always includes: (a) identified energy center(s), (b) brief Joe-language explanation, (c) targeted clearing practice, (d) affirmation/decree for that center's higher expression.

**4. Multi-tenant isolation**
All Supabase queries are scoped to `auth.uid()` via Row Level Security (RLS). No admin queries bypass RLS in application code.

---

## Technology Stack

### Frontend
- **Next.js** — 14+ (App Router) — PWA via `next-pwa`
- **TypeScript** — strict mode
- **Tailwind CSS** — utility-first styling
- **shadcn/ui** — component library

### Backend
- **Next.js API Routes** — server-side AI calls, config CRUD
- **Supabase** — Auth + Postgres database + RLS

### AI
- **Anthropic Claude API** — `claude-sonnet-4-5` (or latest) — all content generation
- **Model context:** Framework system prompt (~3-4k tokens) + user config (~500-1k tokens) + user input

### Infrastructure
- **Vercel** — deployment (PWA-compatible)
- **Supabase** — managed Postgres + Auth

### Package Management
- **pnpm** — fast, disk-efficient

---

## Security & Configuration

### Authentication
- Supabase Auth — email/password
- Invite-only: sign-up disabled; invites generate single-use tokens
- JWT stored in Supabase session; all API routes validate session server-side

### Row Level Security
All user data tables enforce RLS:
```sql
-- Example: user_configs table
CREATE POLICY "Users can only access own config"
ON user_configs FOR ALL
USING (auth.uid() = user_id);
```

### Environment Variables
```bash
# Supabase
NEXT_PUBLIC_SUPABASE_URL=
NEXT_PUBLIC_SUPABASE_ANON_KEY=
SUPABASE_SERVICE_ROLE_KEY=   # Server-side only

# Anthropic
ANTHROPIC_API_KEY=            # Server-side only, never exposed to client

# App
NEXT_PUBLIC_APP_URL=
ADMIN_EMAIL=                  # For invite management
```

---

## User Config Schema

```typescript
interface UserConfig {
  id: string;
  userId: string;
  createdAt: string;
  updatedAt: string;
  
  // Work areas (what the user is actively focusing on)
  workAreas: string[];           // e.g., ["business abundance", "energy center clearing", "mystical experiences"]
  
  // Blueprint patterns being rewired
  limitingBlueprints: string[];  // e.g., ["unworthy of abundance", "must struggle to succeed"]
  
  // Future memories from meditation (stored as first-person sensory descriptions)
  futureMemories: FutureMemory[];
  
  // Elevated emotion anchors
  elevatedEmotionAnchors: string[]; // e.g., ["gratitude", "joy", "awe"]
  
  // Energy centers the user is actively working with
  energyCenterFocus: number[];   // e.g., [2, 3, 4] — center numbers 1-8
  
  // Additional context from onboarding
  additionalContext: string;
}

interface FutureMemory {
  id: string;
  label: string;           // e.g., "Investment close"
  description: string;     // First-person sensory description of the memory
  emotions: string[];      // Emotions present in this memory
}
```

---

## API Specification

### POST /api/generate

Generates aligned content based on action type and user config.

**Request:**
```json
{
  "action": "affirmation" | "daily-joe" | "future-memory" | "struggle",
  "userInput": "string (optional — for struggle flow)",
  "futureMemoryId": "string (optional — for future-memory action)"
}
```

**Response:**
```json
{
  "content": "string",
  "exerciseType": "decree | erasure | future-memory | emotion-anchor | blessing | gratitude | claim",
  "energyCenter": {
    "number": 2,
    "name": "Sacral",
    "explanation": "string",
    "clearingPractice": "string"
  }
}
```

### POST /api/onboarding/message

Handles conversational onboarding — one message at a time.

**Onboarding AI behavior:**
- Target 10-12 questions to build a complete config
- Stop early if sufficient depth is captured before question 10
- Extend up to 20 questions only if user answers are sparse and more is needed for quality config
- Never ask a question just to hit a number — completeness of picture is the exit condition

**Request:**
```json
{
  "messages": [{ "role": "user" | "assistant", "content": "string" }],
  "questionCount": 0
}
```

**Response:**
```json
{
  "message": "string",
  "isComplete": false,
  "extractedConfig": null | "UserConfig (partial)"
}
```

---

## Success Criteria

### MVP Success Definition: **The Product Works**

**Functional Requirements:**
- ✅ User can complete onboarding and have a personal config stored
- ✅ Home screen affirmation is generated and feels personal/grounded in user's work
- ✅ All 4 action buttons return relevant, framework-grounded, personalized content
- ✅ "I'm Struggling" flow identifies an energy center and delivers a targeted practice
- ✅ "Remember My Future" produces a sensory, first-person, "already done" experience
- ✅ User can edit their config in Settings and new content reflects the changes
- ✅ App installs and runs as PWA on iPhone
- ✅ Second user (invited) has fully isolated config and experience

**Quality Indicators:**
- Content reads as Dispenza/FSS-grounded — not generic wellness
- "Remember My Future" content uses past tense / "already done" language consistently
- Energy center diagnostic matches the described struggle accurately
- Response time under 5 seconds for all generation calls

---

## Implementation Phases

### Phase 1: Foundation (Week 1)
**Goal:** Auth, config schema, and basic AI generation working end-to-end

**Deliverables:**
- ✅ Next.js + Supabase project scaffolded with RLS
- ✅ Invite-only auth flow (email)
- ✅ User config schema + CRUD API
- ✅ Framework system prompt written and tested in isolation (Dispenza + FSS)
- ✅ `/api/generate` endpoint working with hardcoded test config
- ✅ Basic home screen UI (no styling — functional only)

**Validation:** Authenticated user can call generate endpoint and receive framework-grounded content.

---

### Phase 2: Onboarding + Config (Week 2)
**Goal:** Conversational onboarding builds a real user config

**Deliverables:**
- ✅ Onboarding conversational UI (one question at a time)
- ✅ Onboarding AI extracts and structures config from conversation
- ✅ Config saved to Supabase on completion
- ✅ Settings page — view and edit config fields
- ✅ Home screen affirmation now pulls from real user config

**Validation:** New user completes onboarding; generated affirmation references their specific work areas and blueprint patterns.

---

### Phase 3: Action Buttons + Struggle Flow (Week 3)
**Goal:** All 4 action buttons working; struggle flow with energy center diagnostic

**Deliverables:**
- ✅ All 4 action buttons implemented and generating personalized content
- ✅ "I'm Struggling" flow: input → energy center ID → targeted practice + reframe
- ✅ "Remember My Future" — pulls from user's stored future memories, delivers sensory recall
- ✅ Exercise engine selecting appropriate FSS exercise type per context

**Validation:** James (solo user) uses all 4 buttons across multiple sessions; content feels personal, grounded, and distinct per button.

---

### Phase 4: PWA + Polish (Week 4)
**Goal:** Installable on iPhone; UX feels intentional

**Deliverables:**
- ✅ PWA manifest + service worker configured
- ✅ App installs cleanly on iPhone/iPad home screen
- ✅ UI styled — calm, intentional, aligned with the nature of the tool
- ✅ Invite flow for second user tested end-to-end
- ✅ Loading states, error handling, edge cases addressed

**Validation:** App installed on James's iPhone; second invited user completes onboarding with fully isolated experience.

---

## Future Considerations (Post-MVP)

- Push notifications / proactive reach-outs (morning alignment nudge)
- Audio integration — guided micro-meditations for energy center clearing
- React Native conversion for better native feel + App Store distribution
- Community features — shared affirmations, group practice feed
- Streak tracking / practice consistency visualization
- Integration with Joe's official meditation catalog (deep links)
- Expanded FSS content — chapter-specific exercises from The Game of Life
- Multi-language support

---

## Risks & Mitigations

### Risk: AI content quality / framework drift
Content may drift toward generic wellness language or misrepresent the Dispenza framework.
**Mitigation:** System prompt is carefully authored with specific language constraints. "Daily Dose of Joe" content is reviewed before ship. Ongoing prompt refinement as James uses the tool.

### Risk: "Remember My Future" language consistency
The past-tense / "already done" framing is easy to slip out of under generation.
**Mitigation:** Explicit constraint in system prompt. Separate validation pass in generation for this action type. James reviews output quality during Phase 3.

### Risk: Onboarding too long / users drop off
Even a well-designed conversational onboarding can feel long if not managed carefully.
**Mitigation:** AI targets 10-12 questions as the sweet spot — stops early if sufficient config depth is achieved. Only extends toward the 20-question ceiling if user answers are sparse and the AI needs more to build a quality config. User can always complete/refine in Settings at any time.

### Risk: PWA limitations on iOS
iOS PWA support is improving but still has gaps (push notifications, background sync).
**Mitigation:** v1 scope avoids all PWA-unsupported features. React Native path preserved for v2 if needed.

---

---

## UI/UX Specification

### Selected Design Direction
**"Morning Light" base + Signal energy center card**

Warm, grounded, daily-ritual aesthetic. Cards and tiles. Lora serif for display/affirmation text. Plus Jakarta Sans for UI/body. Cream/linen background. Sage green primary accent. Amber gold for featured elements. The app feels like a morning practice journal — safe, intentional, beautiful but never precious.

Reference mockup: `projects/affirm/design/mockups/concept-final-hybrid.html`
User flow diagram: `projects/affirm/design/affirm-user-flow.excalidraw`

---

### Color Palette

```css
/* Backgrounds */
--bg-app:        #FAF7F2;   /* warm linen — app background */
--bg-body:       #DDD5C8;   /* outer body / desktop wrapper */

/* Surfaces */
--surface-card:  #FFFFFF;   /* cards, tiles, result containers */
--surface-alt:   #F5F0E8;   /* featured tile background */

/* Borders & Dividers */
--border-soft:   #E8E0D4;   /* card borders, input borders */
--border-card:   rgba(80,60,40,0.04); /* card ring shadow */
--divider:       #F0EBE3;   /* in-card dividers */

/* Accent — Sage Green (primary CTA, tags, accents) */
--accent:        #5C7A5C;   /* primary buttons, filled CTAs */
--accent-light:  #EDF3E8;   /* tag backgrounds, icon boxes */
--accent-text:   #4A6B4A;   /* text on light accent backgrounds */
--accent-dark:   #3D5C3D;   /* memory header card, dark sage */

/* Accent — Amber Gold (featured / Remember My Future) */
--gold:          #D4A853;   /* featured tile border */
--gold-dark:     #B8892A;   /* center badge gradient, gold text */
--gold-light:    #FDF5E0;   /* icon box background */
--gold-badge:    #FFF3CD;   /* exercise type badge background */
--gold-badge-text: #B8892A;

/* Text */
--text-primary:  #2D2420;   /* headings, body text */
--text-secondary:#6B5F54;   /* supporting body text */
--text-muted:    #A89884;   /* labels, timestamps, hints */
--text-faint:    #B0A494;   /* status bar, greeting sub */
--text-caption:  #C4B8A8;   /* card meta, source lines */

/* Semantic */
--error:         #C0392B;
--success:       #4A6B4A;
```

---

### Typography

**Font Stack:**
```css
--font-display: 'Lora', Georgia, 'Times New Roman', serif;
--font-ui:      'Plus Jakarta Sans', -apple-system, BlinkMacSystemFont, sans-serif;
```

**Scale:**
| Role | Size | Weight | Font | Line Height |
|------|------|--------|------|-------------|
| Greeting (app name) | 22px | 400 | Lora | 1.2 |
| Affirmation / Pull Quote | 20–22px | 400 | Lora | 1.56 |
| Memory Text | 19px | 400 | Lora | 1.65 |
| Result Quote | 22px | 400 | Lora | 1.56 |
| Section Title | 15px | 600 | Plus Jakarta Sans | 1.2 |
| Body / Context | 14px | 400 | Plus Jakarta Sans | 1.6 |
| Action Tile Title | 13px | 600 | Plus Jakarta Sans | 1.3 |
| Labels / Badges | 10–11px | 600 | Plus Jakarta Sans | 1.0 |
| Caption / Meta | 12px | 400 | Plus Jakarta Sans | 1.4 |
| Button | 14–15px | 600 | Plus Jakarta Sans | 1.0 |

**Italic use:** Lora italic (`font-style: italic`) for emphasized words within affirmations and pull quotes. Color: `--accent-text` (`#5C7A5C`) or `#4A6B4A`.

**Google Fonts import:**
```html
<link href="https://fonts.googleapis.com/css2?family=Lora:ital,wght@0,400;0,500;0,600;1,400;1,500&family=Plus+Jakarta+Sans:wght@300;400;500;600;700&display=swap" rel="stylesheet">
```

---

### Spacing System (8pt grid)

```css
--px:    20px;   /* horizontal screen padding */
--gap-sm: 8px;
--gap-md: 12px;
--gap-lg: 16px;
--gap-xl: 20px;
--gap-2xl: 24px;

--radius-sm:  10px;
--radius-md:  14px;
--radius-lg:  18px;
--radius-xl:  20px;
--radius-2xl: 24px;
--radius-phone: 44px;
```

---

### iOS PWA Requirements

```html
<meta name="viewport" content="width=device-width, initial-scale=1.0, viewport-fit=cover">
<meta name="apple-mobile-web-app-capable" content="yes">
<meta name="apple-mobile-web-app-status-bar-style" content="default">
```

```css
/* Safe areas */
.safe-top    { height: env(safe-area-inset-top, 44px); }
.safe-bottom { height: env(safe-area-inset-bottom, 34px); }

/* iOS tap suppression */
button, a, [role="button"] {
  -webkit-tap-highlight-color: transparent;
  min-height: 44px;
  min-width: 44px;
}
```

---

### Screen Inventory & Specifications

#### 1. Home Screen

**Layout:** Greeting header → Affirmation card → "What do you need?" label → 2×2 action tile grid

**Components:**
- **Greeting header** (padding: 4px 24px 0)
  - Left: greeting sub (`Good morning, [name]`, 12px muted) + greeting main (`Your Alignment`, 22px Lora)
  - Right: Settings button (42×42px, border `--border-soft`, radius 13px, icon `⚙`)

- **Affirmation card** (margin: 18px 20px 0, bg: white, radius: 24px, padding: 26px 22px 22px)
  - Label: `TODAY'S BLUEPRINT` — 10px, 600wt, letter-spacing 0.18em, color `--accent-text`
  - Text: 20px Lora, line-height 1.56, color `--text-primary`. Key words in Lora italic, color `--accent-text`
  - Divider: 1px `--divider`
  - Footer row: source meta (12px `--text-caption`) left, `New ↻` refresh button (13px `--text-muted`) right

- **Section label:** `WHAT DO YOU NEED?` — 11px, 600wt, letter-spacing 0.14em, `--text-caption`, padding: 20px 24px 12px

- **2×2 Action Tile Grid** (padding: 0 20px, gap: 10px)
  - Default tile: bg white, radius 20px, padding 18px 16px, border 1.5px transparent, shadow subtle. Min-height: 96px
  - Featured tile ("Remember My Future"): bg linear-gradient(`#F7F3EC`, `#F2EDE4`), border `--gold`
  - Each tile: icon box (34×34px, radius 10px) + tile title (13px, 600wt)
  - Icon box colors: green `#EDF3E8`, sage `#E9EFEA`, amber `#FDF5E0`, terra `#F0EDE8`
  - Active state: `transform: scale(0.97)`

**Action Tiles (in order, left-to-right, top-to-bottom):**
1. ✦ **Positive Affirmation** — icon-green
2. ◈ **Daily Dose of Joe** — icon-sage
3. ◉ **Remember My Future** — icon-amber (featured styling)
4. ♡ **Feeling Out of Alignment** — icon-terra

**No check-in strip.** The "Feeling Out of Alignment" tile is the sole entry point for the struggle flow.

---

#### 2. Positive Affirmation Screen

**Header:** Back button + "Affirmation" title
**Content:**
- Exercise type badge (amber: `FFF3CD` bg, `--gold-dark` text) — e.g., "✦ Gratitude Treatment"
- Pull quote: 22px Lora, line-height 1.56, italic accents in `--accent-text`
- Context card (white, radius 16px, shadow subtle):
  - Label: `FROM FLORENCE SCOVEL SHINN` — 10px, 600wt, `--text-caption`
  - Body: 14px, line-height 1.6, `--text-secondary`

**Footer:** `Again ↻` outline btn + `I receive this` filled btn (sage green)

---

#### 3. Daily Dose of Joe Screen

**Header:** Back button + "Daily Dose of Joe" title
**Content:**
- Source badge (green: `#EDF3E8` bg, `--accent-text` text) — e.g., "📖 Dr. Joe · Becoming Supernatural"
- Pull quote: 22px Lora, italic accents in `--accent-text`
- Context card:
  - Label: `WHY THIS MATTERS FOR YOUR WORK`
  - Body: 14px explanation grounded in user's config
  - Save hint: 11px italic `--text-muted` — "Save this to your personal library from Settings →"

**Footer:** `Save` outline btn + `I receive this` filled btn
**Save behavior:** Stores teaching to user's saved library (accessible in Settings). V1 can be simple array in user config.

---

#### 4. Remember My Future Screen

**Header:** Back button + "Remembering Your Future" title

**Memory Header Card** (margin: 16px 20px 0, bg: linear-gradient(`#4A6B4A`, `#3D5C3D`), radius 20px, padding: 20px 22px):
- Label: `FUTURE MEMORY` — 10px, 600wt, letter-spacing 0.18em, white 55% opacity
- Title: 20px Lora, white — the memory name (e.g., "The Investment Close")
- Sub: 12px, white 50% opacity — "Remembered · Already done"

**Memory Body** (padding: 22px 24px 0):
- Text: 19px Lora, line-height 1.65, `--text-primary`. Key phrases in `--accent-dark` (`#3D5C3D`), weight 500
- Written in **first person, past tense** — as if recalling an already-experienced memory. Never future tense. Never "I want."
- Emotion tags row: flex wrap, tags with `#EDF3E8` bg, `--accent-text` color, 12px, 500wt, radius 20px

**Footer:** Single full-width `I remember this` button (sage green)

**Language constraint (enforced in AI prompt):** Every word of the memory text must treat the event as already experienced. Trigger phrases: "I remember...", "I felt...", "I knew...", "In that moment...", "I sat in the quiet...". Never: "I will...", "I want...", "When I receive..."

---

#### 5. Feeling Out of Alignment Screen

**Header:** Back button + "Coming Back to Alignment" title

**Body:**
- Intro: 21px Lora, `--text-primary` — "What's pulling you out?"
- Sub: 14px, `--text-muted` — "Name it honestly. This is how we find the way back."
- **Text input area:** bg white, border 1.5px `--border-soft`, radius 16px, padding 15px 16px, min-height 96px, 15px Plus Jakarta Sans

- **Energy Center Card** (Signal-style, bg white, radius 18px, border `--border-soft`):
  - **Header section** (bg: linear-gradient(`#F5F0E8`, `#EDE8DF`), padding 14px 16px, border-bottom `--border-soft`):
    - Center badge: 38×38px, radius 11px, bg linear-gradient(`#B8892A`, `#D4A853`), white bold number, 16px
    - Center name: 14px, 600wt, `--text-primary` — e.g., "Center 2 · Sacral — Worthiness"
    - Center sub: 12px, `--text-muted` — e.g., "Guilt, shame, and victim mentality patterns"
  - **Body section** (padding 14px 16px): 13px, line-height 1.6, `--text-secondary` — full explanation connecting center to user's config and struggle

**Footer:** `Different` outline btn + `Show me the way back` filled btn → navigates to Alignment Response screen

---

#### 6. Alignment Response Screen (Your Way Back)

**Header:** Back button (returns to struggle input) + "Your Way Back" title

**Body** (scrollable, padding 16px 20px 0, 3 sections):

**Section 1 — Reframe:**
- Label: `REFRAME` — 10px, 600wt, letter-spacing 0.16em, `--text-caption`
- Card (white, radius 18px, shadow subtle, padding 20px):
  - Text: 18px Lora, line-height 1.6, `--text-primary`. Key phrases in Lora italic, `--accent-text`
  - Source line: 11px, `--text-caption` — grounded in Joe's teaching

**Section 2 — Your Exercise:**
- Label: `YOUR EXERCISE · [EXERCISE TYPE]`
- Card (white, radius 18px, padding 18px 20px):
  - Exercise type badge (amber)
  - **Instruction line:** 12px, `--text-muted`, line-height 1.6 —
    *"Speak these words **out loud, slowly — 3 times.** With each repetition, feel the truth of them in your body, not just your mind. The emotion is the signal. The words are the carrier."*
    ("3 times" and "out loud, slowly" in bold `--text-secondary`)
  - Exercise text: 17px Lora italic, line-height 1.6, `--text-primary` — the actual FSS exercise words

**Section 3 — Energy Center Clearing:**
- Label: `CENTER [N] CLEARING`
- Card (bg: linear-gradient(`#EDF3E8`, `#E8EFEA`), radius 18px, padding 16px 18px):
  - Title: 13px, 600wt, `#3D5C3D`
  - Body: 13px, line-height 1.6, `--accent-text` — the specific clearing practice for the identified center

**Footer:** Full-width `I'm back in alignment` button (sage green) → navigates to Home

---

#### 7. Settings / Your Blueprint Screen

**Header:** Back button + "Your Blueprint" title

**Body** (scrollable, 4 settings cards, gap 12px, padding 16px 20px):

Each card: bg white, radius 20px, padding 18px 20px, shadow subtle

1. **Work Areas** — tag row of sage green pills
2. **Blueprint to Rewire** — italic Lora text, `--text-secondary`
3. **Elevated Emotion Anchors** — tag row of sage green pills
4. **Future Memories** — list of memory rows (title + emotion tags + chevron)

**Footer:** Full-width `Save Blueprint` button

---

### Component Library

#### Buttons

```css
/* Filled (primary CTA) */
.btn-filled {
  background: #5C7A5C;
  color: white;
  border: none;
  border-radius: 14px;
  padding: 14px 20px;
  font-size: 14px;
  font-weight: 600;
  min-height: 48px;
  font-family: var(--font-ui);
  transition: transform 0.1s, opacity 0.1s;
}
.btn-filled:active { transform: scale(0.97); opacity: 0.9; }

/* Full width variant */
.btn-full { width: 100%; padding: 16px; font-size: 15px; min-height: 52px; }

/* Outline (secondary) */
.btn-outline {
  background: white;
  border: 1.5px solid #E0D8CE;
  color: #8A7B6B;
  border-radius: 14px;
  padding: 14px 20px;
  font-size: 14px;
  font-weight: 500;
  min-height: 48px;
  font-family: var(--font-ui);
}
.btn-outline:active { transform: scale(0.97); background: #FAF7F2; }
```

#### Back Button
```css
.back-btn {
  width: 44px; height: 44px;
  background: white;
  border: 1.5px solid #E8E0D4;
  border-radius: 13px;
  color: #8A7B6B;
  font-size: 18px;
}
```

#### Cards
```css
.card {
  background: white;
  border-radius: 20px;
  padding: 18px 20px;
  box-shadow: 0 1px 8px rgba(80,60,40,0.05), 0 0 0 1px rgba(80,60,40,0.03);
}
```

#### Source/Exercise Badges
```css
.badge {
  display: inline-flex;
  align-items: center;
  border-radius: 20px;
  padding: 6px 12px;
  font-size: 11px;
  font-weight: 600;
  letter-spacing: 0.06em;
}
.badge-green { background: #EDF3E8; color: #4A6B4A; }
.badge-amber { background: #FFF3CD; color: #B8892A; }
```

#### Emotion/Work Area Tags
```css
.tag {
  padding: 7px 14px;
  background: #EDF3E8;
  border-radius: 20px;
  font-size: 12px;
  font-weight: 500;
  color: #4A6B4A;
}
```

---

### Animation & Transition Spec

```css
/* Screen transitions — none in v1, screens swap instantly */
/* Button active states */
transition: transform 0.1s ease, opacity 0.1s ease;

/* Card hover (desktop/iPad) */
.action-tile:hover { box-shadow: 0 4px 16px rgba(80,60,40,0.1); }
```

No page transition animations in v1. Screens swap immediately on tap. Fast = aligned with the tool's purpose (state shift, not animation showcase).

---

### Accessibility

- All interactive elements: min 44×44px touch target
- Text contrast: all body text verified at 4.5:1+ against backgrounds
- Semantic HTML: `<button>` for actions, `<h1>`/`<h2>` for screen titles, `<label>` for inputs
- `aria-label` on icon-only buttons (settings, back)
- `prefers-reduced-motion`: no animations active by default anyway

---

## Appendix

### Source Teachings
- Dr. Joe Dispenza — *Becoming Supernatural*, *Breaking the Habit of Being Yourself*, Abundance Course transcripts (Videos 1-19)
- Florence Scovel Shinn — *The Game of Life and How to Play It*

### Meditation Catalog Reference
Per Dr. Joe's catalog: Generating Series, Morning/Evening, Changing Boxes, Tuning Into New Potentials, Changing Beliefs and Perceptions, The Generous Present Moment, BOTEC (Levels 1-12), Synchronize Your Energy Series, Pineal Gland Meditation, Walking Meditations.

*Note: The app references these meditations contextually (e.g., "Daily Dose of Joe" may reference a relevant practice). The app does not play or reproduce any official meditation content.*
