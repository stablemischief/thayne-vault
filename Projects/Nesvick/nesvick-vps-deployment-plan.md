---
project: Nesvick
source: thane
imported: 2026-04-07
---
# Nesvick VPS Deployment Plan
**Created:** 2026-03-17
**Target:** Coolify on Hostinger VPS
**Repo:** Stable-Mischief-AI/open-notebook-nesvick

---

## Current State
- App is fully functional locally on MacBook Pro (Docker Compose)
- Clerk auth working (sign-in + access code gate)
- Podcast generation working (transcript + audio via ElevenLabs)
- Supabase: PRD written, NOT yet implemented
- Models: up to date (Anthropic 4.6, ElevenLabs v3/flash)

---

## Pre-Deployment Blockers (must complete before VPS launch)

### BLOCKER 1: Supabase Archive Layer
The public podcast URL (sent to newsletter subscribers) requires Supabase Storage.
Without it, there is no shareable link for audio playback.

**Tasks:**
- [ ] Create Supabase project (nesvick-podcast)
- [ ] Create storage bucket: `podcast-episodes` (public)
- [ ] Create Postgres table: `episodes` (id, title, date, audio_url, transcript_url, created_at)
- [ ] Implement "Generate Podcast URL" button feature (Archon task)
- [ ] Add Supabase env vars to .env: `SUPABASE_URL`, `SUPABASE_ANON_KEY`, `SUPABASE_SERVICE_KEY`
- [ ] Test end-to-end: generate podcast → click button → get public URL

### BLOCKER 2: Domain Configuration
`NEXT_PUBLIC_API_URL` is baked into the Next.js build at compile time.
It must be set to the public domain, not localhost.

**Tasks:**
- [ ] Decide on domain/subdomain (e.g. `app.nesvick.com`)
- [ ] Point DNS to Hostinger VPS IP
- [ ] Confirm domain in Clerk Dashboard → allowed origins

---

## Phase 1: Supabase Setup (Start Here)

### Step 1.1 — Create Supabase Project
1. Go to supabase.com → New Project
2. Name: `nesvick-podcast`
3. Region: closest to Hostinger VPS location
4. Save: Project URL, anon key, service role key

### Step 1.2 — Create Storage Bucket
```sql
-- In Supabase SQL editor:
INSERT INTO storage.buckets (id, name, public) VALUES ('podcast-episodes', 'podcast-episodes', true);
```

### Step 1.3 — Create Episodes Table
```sql
CREATE TABLE episodes (
  id UUID DEFAULT gen_random_uuid() PRIMARY KEY,
  episode_id TEXT NOT NULL,
  title TEXT,
  episode_date DATE,
  audio_url TEXT,
  transcript_url TEXT,
  duration_seconds INTEGER,
  created_at TIMESTAMP WITH TIME ZONE DEFAULT NOW()
);
```

### Step 1.4 — Implement Archive Feature (Archon Task)
Brief Archon to implement:
- New FastAPI endpoint: `POST /api/podcasts/episodes/{id}/archive`
- Uploads audio file to Supabase Storage
- Writes metadata to Postgres
- Returns public audio URL
- New button in episode UI: "Generate Podcast URL"
- PRD reference: `.agents/plans/supabase-archive-prd.md`

---

## Phase 2: Coolify Setup on Hostinger VPS

### Step 2.1 — Install Coolify
SSH into Hostinger VPS and run:
```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```
Access Coolify UI at `http://<VPS_IP>:8000`

### Step 2.2 — Add Server
- Coolify UI → Servers → Add Server
- Type: Local (if installing on same VPS)

### Step 2.3 — Create New Application
- Source: GitHub → Stable-Mischief-AI/open-notebook-nesvick
- Build Pack: Docker Compose
- Docker Compose file: `docker-compose.yml`
- Branch: `main`

---

## Phase 3: Application Configuration in Coolify

### Step 3.1 — Build Arguments (CRITICAL)
These are baked into the Next.js build — must be set before first deploy:
```
NEXT_PUBLIC_API_URL=https://app.nesvick.com
```

### Step 3.2 — Environment Variables
Set ALL of these in Coolify → Environment Variables:
```
# Clerk Auth
CLERK_PUBLISHABLE_KEY=pk_live_...
CLERK_SECRET_KEY=sk_live_...
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_...
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in

# App
APP_ACCESS_CODE=38138
OPEN_NOTEBOOK_ENCRYPTION_KEY=<same as local>

# SurrealDB
SURREAL_URL=ws://surrealdb:8000/rpc
SURREAL_USER=root
SURREAL_PASSWORD=<secure password>
SURREAL_NAMESPACE=open_notebook
SURREAL_DATABASE=open_notebook

# AI Providers
ANTHROPIC_API_KEY=sk-ant-...
ELEVENLABS_API_KEY=...
OPENAI_API_KEY=sk-...

# Supabase (after Phase 1)
SUPABASE_URL=https://xxx.supabase.co
SUPABASE_ANON_KEY=...
SUPABASE_SERVICE_KEY=...
```

### Step 3.3 — Port Configuration
Coolify should expose only port 8502 (Next.js frontend) publicly.
Port 5055 (FastAPI) should NOT be publicly exposed — internal only.

### Step 3.4 — Volume Mounts (DATA PERSISTENCE — CRITICAL)
```
./surreal_data:/mydata          # SurrealDB database
./notebook_data:/app/data       # Notebooks, audio files
```
These MUST be configured before first deploy or all data will be lost on restart.

### Step 3.5 — SSL / Domain
- Coolify → Application → Domains → Add domain: `app.nesvick.com`
- Enable: Let's Encrypt (auto SSL)
- Verify DNS is pointed to VPS IP first

---

## Phase 4: Clerk Dashboard Updates

### Step 4.1 — Switch to Production Keys
Current: Clerk dev keys (pk_test_...)
Needed: Clerk production keys (pk_live_...)
- Clerk Dashboard → nesvick-podcast → API Keys → Production

### Step 4.2 — Add Production Domain to Clerk
- Clerk Dashboard → Domains → Add: `app.nesvick.com`
- Update allowed redirect URLs

---

## Phase 5: Deploy & Validate

### Step 5.1 — First Deploy
- Coolify → Deploy
- Watch build logs — Next.js build takes ~3-5 minutes
- Verify container starts cleanly

### Step 5.2 — Smoke Tests
- [ ] `https://app.nesvick.com` loads sign-in page
- [ ] Google OAuth sign-in works
- [ ] Access code `38138` accepted
- [ ] Notebooks page loads (no 401 errors)
- [ ] Can create a notebook
- [ ] Can add a source
- [ ] Podcast generation works end-to-end
- [ ] "Generate Podcast URL" returns public Supabase URL

### Step 5.3 — Newsletter Integration
- [ ] Test public audio URL plays in browser
- [ ] Embed in newsletter template
- [ ] Test in email client

---

## Recommended Order of Operations

```
Week 1: Supabase setup + archive feature implementation
Week 2: Coolify install, domain setup, Clerk production keys
Week 3: Deploy, smoke test, fix any issues
Week 4: Newsletter integration, first live episode
```

---

## Open Questions
- [ ] What domain/subdomain? (app.nesvick.com, podcast.nesvick.com, etc.)
- [ ] Does Hostinger VPS already have Docker installed?
- [ ] Is Coolify already installed or starting fresh?
- [ ] Use Clerk dev keys for initial VPS testing, or go straight to production keys?
- [ ] SurrealDB password — use same as local or set new secure one for production?

---

## Notes
- `NEXT_PUBLIC_API_URL` is a build-time arg — changing it requires a full rebuild
- SurrealDB data volumes are critical — losing them means losing all notebooks, speakers, episode profiles
- The Supabase archive feature is what enables the newsletter use case — without it, audio lives only on the VPS
- Port 5055 should never be publicly exposed in production (Next.js middleware is the auth gate)

---

## Decisions Made (2026-03-17)
- **Domain:** `nesvick.podcast.stablemischief.ai`
- **Coolify:** NOT yet installed on Hostinger VPS — must install as first step
