---
project: Nesvick
source: thane
imported: 2026-04-07
---
# Nesvick VPS Deployment — Full Log & Status
**Last Updated:** 2026-03-20
**App:** `nesvick.podcast.stablemischief.ai`
**Repo:** `Stable-Mischief-AI/open-notebook-nesvick`

---

## What Nesvick Is

Nesvick is a private client deployment of **Open Notebook** (upstream: `lfnovo/open_notebook`, forked as `Stable-Mischief-AI/Stable-Notebook`). It adds:

- **Clerk authentication** with Google OAuth + access code gate (`38138`)
- **Custom episode + speaker profiles** for the Nesvick podcast
- **ElevenLabs TTS** with per-speaker voice settings (stability, style, speed)
- **Custom Anthropic model list** with latest Claude models
- Coolify-managed deployment on a Hostinger VPS

---

## Infrastructure

| Component | Value |
|-----------|-------|
| VPS Host | Hostinger KVM 2 |
| VPS Spec | 2 vCPU, 8GB RAM, 100GB NVMe |
| OS | Ubuntu 24.04 LTS |
| VPS IP | `187.124.231.206` |
| Coolify UI | `http://187.124.231.206:8000` |
| Domain | `nesvick.podcast.stablemischief.ai` |
| DNS Registrar | GoDaddy (domain), **Bluehost nameservers** (ns1/ns2.bluehost.com) |
| Domain owner | Mitch Dornich manages Bluehost |
| Clerk project | `Nesvick-Podcast` (production) |
| Clerk publishable key | `pk_live_Y2xlcmsubmVzdmljay5wb2RjYXN0LnN0YWJsZW1pc2NoaWVmLmFpJA` |
| GCP project | `NESVIC` (Google OAuth) |

---

## Repository Structure (Key Files)

```
open-notebook-nesvick/
├── docker-compose.yml               # Production compose file
├── SYNC.md                          # How to sync from Stable-Notebook upstream
├── seed/
│   ├── nesvick-podcast-templates.surql   # Episode + speaker profile seed data
│   ├── import-profiles.sh               # Runs the seed via API
│   └── update-tts-config.sh             # Sets TTS voice settings via API
├── api/
│   ├── clerk_auth.py                # Clerk JWT verification (Nesvick-specific)
│   └── routers/clerk_auth.py        # /api/auth/verify-access-code endpoint
└── frontend/src/
    ├── middleware.ts                 # Clerk middleware (Nesvick-specific)
    └── app/(auth)/                  # Sign-in/sign-up pages
```

**Files that must NEVER be overwritten during upstream syncs:**
- `api/clerk_auth.py`
- `api/routers/clerk_auth.py`
- `frontend/src/middleware.ts`
- `frontend/src/app/(auth)/`
- `frontend/src/app/access-code/`
- `frontend/src/components/auth/`
- `SYNC.md`
- `seed/nesvick-podcast-templates.surql`

---

## Merged PRs (Full History)

| PR | Branch | Description |
|----|--------|-------------|
| #1 | `feat/clerk-auth` | Initial Clerk auth implementation (Google OAuth + access code) |
| #2–#13 | various | Various features, fixes, model updates synced from Stable-Notebook |
| #14 | `task-feat-add-latest-anthropic-models` | Added 3 new Anthropic models (claude-opus-4-6, sonnet-4-6, haiku-4-5) |
| #16 | `feat/tts-config-speaker-profile` | Added `tts_config` field to SpeakerProfile model |
| #17 | `fix/surrealdb-tts-config-migration` | SurrealDB migration 16: define `tts_config` as FLEXIBLE TYPE in schema |
| #18 | `fix/migration-16-speakers-flexible` | Fixed migration 16: added FLEXIBLE TYPE on `speakers[*]` parent field |
| #19 | `fix/migration-17-speakers-flexible` | Migration 17: re-applies FLEXIBLE fix for DBs that ran old migration 16 |
| #20 | `fix/middleware-public-url` | Fixed middleware to use `x-forwarded-host`/`x-forwarded-proto` headers instead of `req.url` (which resolved to `0.0.0.0:8502` inside Docker) |

---

## VPS Setup — What Was Done

### 1. Provisioned VPS
- Hostinger KVM 2 plan ($8.99/mo)
- Chose "Ubuntu 24.04 plain OS" — NOT the Docker or control panel options
- Plain OS lets Coolify manage everything

### 2. Installed Coolify
```bash
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```
- Accessible at `http://187.124.231.206:8000`
- Created admin account

### 3. Added VPS as Server in Coolify
- **Critical gotcha:** The server field resets to `localhost` — must confirm IP saved before clicking Validate
- SSH key `gentle-gannet-cp379sk7wpcr650g4strmmo9` must be added to VPS:
```bash
docker exec coolify ssh-keygen -y -f /var/www/html/storage/app/ssh/keys/ssh_key@gentle-gannet-cp379sk7wpcr650g4strmmo9 >> /root/.ssh/authorized_keys
```
- **Note (2026-03-20):** Ultimately used `localhost` server (not a separate remote entry) — Coolify on a self-hosted VPS can manage itself via localhost. This is the correct architecture.

### 4. Created GitHub App in Coolify
- Name: `nesvick-github`
- Organization: `Stable-Mischief-AI`
- Authorized repo: `open-notebook-nesvick`

### 5. Created Coolify Stack
- Project: `Nesvick-Podcast`
- Environment: `production`
- Resource: `nesvick-podcast` (Docker Compose stack)
- Source: `nesvick-github` → branch `main`
- Build Pack: Docker Compose
- Base Directory: `/`
- Docker Compose Location: `/docker-compose.yml`

### 6. Set Environment Variables in Coolify

**Buildtime + Runtime** (needed during Next.js compile):
```
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_...
NEXT_PUBLIC_API_URL=https://nesvick.podcast.stablemischief.ai
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
```

**Runtime Only:**
```
CLERK_PUBLISHABLE_KEY=pk_live_...
CLERK_SECRET_KEY=sk_live_...
APP_ACCESS_CODE=38138
OPEN_NOTEBOOK_ENCRYPTION_KEY=<copied from MacBook Pro .env>
SURREAL_URL=ws://surrealdb:8000/rpc
SURREAL_USER=root
SURREAL_PASSWORD=<changed from root>
SURREAL_NAMESPACE=open_notebook
SURREAL_DATABASE=open_notebook
API_URL=http://127.0.0.1:5055
ANTHROPIC_API_KEY=...
ELEVENLABS_API_KEY=...
OPENAI_API_KEY=...
```

### 7. DNS Setup
- A record `nesvick.podcast` → `187.124.231.206` (in Bluehost via Mitch)
- Traefik (Coolify's built-in proxy) handles SSL termination via Let's Encrypt

### 8. Clerk Production Setup
- Created production instance for `Nesvick-Podcast` in Clerk Dashboard
- Configured Google OAuth with custom credentials (GCP project: `NESVIC`)
- Authorized Redirect URI: `https://clerk.nesvick.podcast.stablemischief.ai/v1/oauth_callback`
- Domain added in Clerk: `nesvick.podcast.stablemischief.ai`

### 9. Deployed
- Coolify → Deploy → watched build logs
- Both containers confirmed running:
  - `open_notebook-ariwcruxbluch5x2wugobkmc-*`
  - `surrealdb-ariwcruxbluch5x2wugobkmc-*`

---

## Current Status (2026-03-20)

### ✅ Working
- VPS provisioned and running
- Coolify managing the stack
- Traefik routing confirmed — `wget http://10.0.2.4:8502` from inside coolify-proxy returns Next.js HTML
- SSL cert issued by Let's Encrypt
- Sign-in page loads at `https://nesvick.podcast.stablemischief.ai`
- PR #20 middleware fix (`x-forwarded-host`) IS in the deployed bundle (confirmed via grep)
- All production Clerk keys (`pk_live_` / `sk_live_`) set in Coolify

### ✅ FULLY DEPLOYED — All Issues Resolved (2026-03-22)

### Previously Resolved Issues
**Problem:** Clerk requires 5 CNAME records to be added to DNS. Currently 0/5 verified.

**Required CNAMEs** (add in Bluehost → stablemischief.ai zone):

| Name | Type | Target |
|------|------|--------|
| `clerk.nesvick.podcast` | CNAME | `frontend-api.clerk.services` |
| `accounts.nesvick.podcast` | CNAME | `accounts.clerk.services` |
| `clkmail.nesvick.podcast` | CNAME | `mail.e667bmzqse08.clerk.services` |
| `clk._domainkey.nesvick.podcast` | CNAME | `dkim1.e667bmzqse08.clerk.services` |
| `clk2._domainkey.nesvick.podcast` | CNAME | `dkim2.e667bmzqse08.clerk.services` |

**Action needed:** Mitch Dornich has Bluehost access — contacted to add these records.
**After Mitch adds records:** Go to Clerk Dashboard → Domains → "Verify configuration".

### ⚠️ Secondary Issue — Black Screen on Sign-In
After Clerk DNS is verified, if the black screen on sign-in persists, investigate:
1. Browser console errors — likely a `redirect_url` or Clerk init error
2. Check if `NEXT_PUBLIC_API_URL` is set correctly (`https://nesvick.podcast.stablemischief.ai`)
3. May require a Force Rebuild (no-cache) in Coolify if env vars changed

---

## Remaining Tasks

### Immediate
- [x] Clerk DNS verified (Mitch added 5 CNAMEs in Bluehost) — 2026-03-21
- [x] Sign-in flow working end-to-end on all devices — 2026-03-22
- [x] PR #21 merged — removed `API_URL=http://127.0.0.1:5055` from docker-compose.yml (mixed content fix)

### Seeding Data on VPS
- [ ] Run `seed/update-tts-config.sh` with VPS URL to set TTS voice settings
  - Profile-level: `{stability: 0.35, similarity_boost: 0.80, style: 0.70, speed: 1.15}`
  - Riley per-speaker: `{stability: 0.25, similarity_boost: 0.80, style: 0.80, speed: 1.30}`
- [ ] Verify episode profile briefing includes hallucination prohibition language
- [ ] Confirm speaker profiles show Zachary + Riley with correct ElevenLabs voice IDs:
  - Zachary Davis: `1SM7GgM6IMuvQlz2BwM3`
  - Riley Chen: `aMSt68OGf4xUZAnLpTU8`

### Podcast Quality
- [ ] **Podcast length tuning** — currently ~8.5 min, target 5–6 min
  - Proposed fix: add structural constraint block to episode profile briefing
  - Constraint: 4 exchanges × 2 turns × 35 words/turn = 840 words total → ~4.5–5.5 min at 180 wpm
  - Apply in UI → test generation → adjust if needed
- [ ] **Hallucination fix** — update `default_briefing` to add explicit prohibition:
  > "Do not reference any historical events, dates, statistics, prices, or data that are not explicitly provided in the source material. You are a podcast host summarizing provided content — you do not have independent knowledge of market history."

### Docker Compose Hardening (via Archon PR)
- [ ] Move port `5055` from `ports:` to `expose:` (reduces attack surface)
- [ ] Change SurrealDB credentials from `root:root` hardcoded in compose to env vars
- [ ] These are not breaking issues but should be cleaned up before calling it production-ready

### Ongoing Sync (Stable-Notebook → Nesvick)
When Stable-Notebook gets upstream updates:
1. On Mac mini: `cd /Users/jw-dev/Developer/GitHub/open-notebook-nesvick`
2. `git fetch stable-notebook`
3. `git merge stable-notebook/main --no-ff`
4. Resolve any conflicts in protected files (middleware.ts, clerk_auth.py, etc.)
5. Push to `main` → Coolify auto-deploys

---

## Key Credentials & Identifiers

> ⚠️ Full credentials are in TOOLS.md. This section has non-secret identifiers only.

| Item | Value |
|------|-------|
| VPS IP | `187.124.231.206` |
| App domain | `nesvick.podcast.stablemischief.ai` |
| Coolify UI | `http://187.124.231.206:8000` |
| Coolify stack | Nesvick-Podcast → production → nesvick-podcast |
| App container pattern | `open_notebook-ariwcruxbluch5x2wugobkmc-*` |
| SurrealDB container | `surrealdb-ariwcruxbluch5x2wugobkmc-*` |
| App ports | Web: `8502`, API: `5055` |
| Access code | `38138` |
| Clerk project | `Nesvick-Podcast` (production) |
| GCP project | `NESVIC` |
| Clerk publishable key prefix | `pk_live_Y2xlcmsu...` |
| Zachary voice ID | `1SM7GgM6IMuvQlz2BwM3` |
| Riley voice ID | `aMSt68OGf4xUZAnLpTU8` |
| Episode profile ID (VPS) | TBD — created after seeding |
| Speaker profile ID (Mac mini) | `speaker_profile:kz7k6n5g3hcxw4stgcoq` |

---

## Lessons Learned

1. **Coolify IP field resets silently** — always confirm the server IP saved before clicking Validate
2. **Docker image caching** — `docker compose up` reuses cached images; new code only takes effect after `--build` or Force Rebuild in Coolify
3. **`req.url` inside Docker** — resolves to container's internal address (`0.0.0.0:8502`), not the public URL. Always use `x-forwarded-host` + `x-forwarded-proto` headers from Traefik.
4. **Clerk dev keys blocked on real domains** — `pk_test_` keys only work on localhost; production domains require `pk_live_` keys
5. **SurrealDB SCHEMAFULL + FLEXIBLE TYPE** — `TYPE object` (non-FLEXIBLE) silently drops all sub-fields not explicitly defined; must use `FLEXIBLE TYPE object` for arbitrary nested data like `tts_config`
6. **Migration re-application** — databases that already ran migration 16 won't re-run it even if fixed in a new commit; needed migration 17 to re-apply the FLEXIBLE fix for existing DBs
7. **Coolify localhost server** — on a self-hosted VPS, Coolify manages itself via `localhost`; no need to add the VPS as a separate remote server
8. **Bluehost DNS for stablemischief.ai** — domain registered via GoDaddy but nameservers point to Bluehost; Mitch manages Bluehost access
