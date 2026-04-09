# PRD: Nesvick Shareable Episode URL
**Version:** 1.0  
**Date:** 2026-03-22  
**Status:** Draft — pending UI/UX mockup selection

---

## Overview

Add a "Generate Shareable URL" feature to the Nesvick podcast platform. When a podcast episode is generated, users can upload the episode to Supabase Storage and generate a permanent public URL. The URL opens a minimal branded player page — no authentication required, works on all devices.

---

## Problem Statement

Currently, generated podcast episodes live only inside the Nesvick app and are inaccessible to external stakeholders (clients, subscribers). There is no way to share a finished episode without giving someone access to the Nesvick platform itself.

---

## Goals

1. Enable one-click upload of a finished episode to Supabase Storage
2. Generate a permanent public URL that anyone can access without authentication
3. Serve the episode on a minimal branded player page (`/share/{id}`)
4. Store episode metadata (audio, transcript, newsletter source) in Supabase for future use

---

## Non-Goals (v1)

- Custom branding per client (Nesvick-specific branding only in v1)
- Link expiration or time-limited URLs (purge process deferred)
- RSS feed or podcast directory integration
- Social sharing cards / Open Graph meta tags (deferred)
- Analytics / play tracking (deferred)
- Batch share / bulk upload

---

## User Stories

### US-1: Generate Shareable URL
**As a** Nesvick app user,  
**I want to** click "Generate Shareable URL" on a finished episode,  
**So that** I can share the episode with subscribers without requiring them to log in.

**Acceptance Criteria:**
- "Generate Shareable URL" button appears on episode cards/detail view
- Clicking the button uploads audio to Supabase Storage and saves metadata to Supabase DB
- A URL is returned and displayed in a read-only field
- A "Copy URL" button copies the URL to clipboard with a toast confirmation
- Button shows loading state during upload; shows error if upload fails
- Once generated, the same URL is shown on subsequent views (idempotent — no duplicate uploads)

### US-2: Public Player Page
**As a** recipient of a shared episode link,  
**I want to** open the link and immediately play the podcast,  
**So that** I can listen without signing in or installing anything.

**Acceptance Criteria:**
- `/share/{id}` route is publicly accessible (no Clerk auth required)
- Page loads on all devices: desktop, iPhone, iPad, Android
- Page shows: episode title, date, Nesvick branding, audio player with play/pause/scrub
- Play is user-initiated (not auto-play)
- Page is fast to load (< 2s on mobile)
- Invalid/missing `{id}` shows a simple "Episode not found" message

---

## Technical Architecture

### Supabase Schema

**Table: `shared_episodes`**
```sql
CREATE TABLE shared_episodes (
  id          UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  episode_id  TEXT NOT NULL,           -- SurrealDB episode ID
  title       TEXT NOT NULL,
  created_at  TIMESTAMPTZ DEFAULT NOW(),
  audio_url   TEXT NOT NULL,           -- Supabase Storage public URL
  transcript  TEXT,                    -- Full episode transcript
  source_text TEXT,                    -- Newsletter/source content
  share_url   TEXT NOT NULL            -- Full public share URL
);

CREATE INDEX idx_shared_episodes_episode_id ON shared_episodes(episode_id);
```

**Supabase Storage:**
- Bucket: `podcast-episodes` (public read)
- Path: `episodes/{episode_id}/{filename}.mp3`

### API Endpoints (FastAPI)

**POST `/api/podcasts/episodes/{episode_id}/share`**
- Uploads audio to Supabase Storage
- Saves record to `shared_episodes`
- Returns `{ share_url, audio_url, id }`
- Idempotent: returns existing record if already shared

**GET `/api/podcasts/episodes/{episode_id}/share`**
- Returns existing share record if exists, 404 if not

### Frontend Changes

1. **Episode UI** — "Generate Shareable URL" button + loading state + URL field + "Copy URL" button
2. **New route** — `/share/[id]/page.tsx` (public, no auth, branded player)
3. **Middleware** — Add `/share/(.*)` to `isPublicRoute` in `middleware.ts`

### Environment Variables (add to Coolify)
```
NEXT_PUBLIC_SUPABASE_URL=https://xxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=eyJ...
SUPABASE_SERVICE_ROLE_KEY=eyJ...
```

---

## UI Specification (Pending Mockup Selection)

### Episode Card — Share Section
- Button: "Generate Shareable URL"
- Loading: Spinner + "Uploading..."
- Success: Read-only URL input + "Copy URL" button with toast
- Idempotent: Shows URL immediately if already generated

### Public Player Page (`/share/{id}`)
- Nesvick logo/wordmark top center
- Episode title + date
- Styled audio player (play/pause/scrub/time)
- "Powered by Nesvick" footer
- Minimal, clean — space for future Nesvick branding

---

## Pre-Development Setup (Manual — Before Archon)

1. Create/identify Supabase project
2. Run `shared_episodes` table migration
3. Create `podcast-episodes` Storage bucket (public read)
4. Add Supabase env vars to Coolify

---

## Open Questions (Resolved)

| Question | Decision |
|----------|----------|
| Auth required to view? | No — fully public |
| Permanent or time-limited? | Permanent (purge deferred) |
| Direct audio link vs player page? | Player page at `/share/{id}` |
| Auto-play? | No — play button required |
| Human verification checkbox? | No — play button is sufficient |
| Data stored in Supabase? | Audio URL, transcript, newsletter source, share URL |
