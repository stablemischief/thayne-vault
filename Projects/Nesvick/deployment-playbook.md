# Nesvick Podcast — Complete Deployment Playbook
**Version:** 1.0  
**Date:** 2026-03-23  
**Authors:** Thane (from learnings deploying `nesvick.podcast.stablemischief.ai`)

> **Purpose:** This is the definitive guide for deploying a private client instance of the Nesvick Podcast platform on a fresh VPS, starting from the `open-notebook-nesvick` repo. If the VPS crashes, if we're onboarding a new client, or if we're starting from scratch for any reason — this document covers every step, every gotcha, and every decision that was made the first time.

---

## Table of Contents
1. [Architecture Overview](#1-architecture-overview)
2. [Prerequisites Checklist](#2-prerequisites-checklist)
3. [Phase 1 — Repo Setup](#3-phase-1--repo-setup)
4. [Phase 2 — VPS Provisioning](#4-phase-2--vps-provisioning)
5. [Phase 3 — Coolify Installation](#5-phase-3--coolify-installation)
6. [Phase 4 — DNS Setup](#6-phase-4--dns-setup)
7. [Phase 5 — Clerk Production Setup](#7-phase-5--clerk-production-setup)
8. [Phase 6 — Google OAuth Setup](#8-phase-6--google-oauth-setup)
9. [Phase 7 — Coolify Application Setup](#9-phase-7--coolify-application-setup)
10. [Phase 8 — Environment Variables](#10-phase-8--environment-variables)
11. [Phase 9 — First Deploy](#11-phase-9--first-deploy)
12. [Phase 10 — Post-Deploy Seeding](#12-phase-10--post-deploy-seeding)
13. [Phase 11 — Smoke Tests](#13-phase-11--smoke-tests)
14. [Troubleshooting Guide](#14-troubleshooting-guide)
15. [Ongoing Maintenance](#15-ongoing-maintenance)
16. [Appendix A — Stable-Notebook Backport Checklist](#appendix-a--stable-notebook-backport-checklist)
17. [Appendix B — Critical Files Reference](#appendix-b--critical-files-reference)

---

## 1. Architecture Overview

```
Browser
  │
  ▼
Traefik (Coolify's reverse proxy, handles SSL)
  │
  ▼
Next.js frontend (port 8502, inside Docker container)
  │  ├── Clerk middleware (auth gate)
  │  └── /api/* rewrite proxy → FastAPI
  ▼
FastAPI backend (port 5055, same container, supervisor-managed)
  │
  ▼
SurrealDB (separate container, port 8000 internal only)
```

**Key architectural facts:**
- Single Docker container (`Dockerfile.single`) runs both Next.js AND FastAPI via supervisord
- SurrealDB runs in a separate container on the same Docker network
- Traefik (built into Coolify) handles HTTPS termination and routing
- Port 5055 (API) is NEVER publicly exposed — only Next.js (8502) is routed through Traefik
- Next.js rewrites `/api/*` requests server-side to `localhost:5055` — the browser never calls the API directly
- Clerk handles authentication; the access code gate is a second layer (app-level)

---

## 2. Prerequisites Checklist

Before starting, have the following ready:

### Accounts & Access
- [ ] GitHub org with admin access (for creating private repo + Coolify GitHub App)
- [ ] Clerk account at `clerk.com` (free tier is fine for getting started)
- [ ] Google Cloud Console access (for OAuth credentials)
- [ ] VPS provider account (Hostinger, DigitalOcean, Hetzner, etc.)
- [ ] DNS management access for the target domain

### Credentials to Gather
- [ ] `OPEN_NOTEBOOK_ENCRYPTION_KEY` — generate with: `python3 -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"`
- [ ] `APP_ACCESS_CODE` — any numeric code (e.g., `38138`)
- [ ] AI API keys: `ANTHROPIC_API_KEY`, `ELEVENLABS_API_KEY`, `OPENAI_API_KEY`
- [ ] SurrealDB password — generate a strong one (NOT `root`)

### Domain
- [ ] Subdomain decided (e.g., `app.podcast.yourdomain.com`)
- [ ] Access to DNS management for that domain confirmed

---

## 3. Phase 1 — Repo Setup

> **If redeploying Nesvick:** Skip to Phase 2. The repo already exists at `Stable-Mischief-AI/open-notebook-nesvick`. Just clone it.
>
> **If setting up a NEW client:** Follow these steps to create a new private client repo from Nesvick.

### 3.1 Create the client repo

```bash
# Clone Nesvick as the starting point
git clone git@github.com:Stable-Mischief-AI/open-notebook-nesvick.git <client-name>-notebook
cd <client-name>-notebook

# Remove the Nesvick remote
git remote remove origin

# Create a new private repo on GitHub (via CLI or web UI)
gh repo create Stable-Mischief-AI/<client-name>-notebook --private

# Set the new remote and push
git remote add origin git@github.com:Stable-Mischief-AI/<client-name>-notebook.git
git push -u origin main
```

### 3.2 Add the stable-notebook upstream remote

```bash
git remote add stable-notebook git@github.com:Stable-Mischief-AI/Stable-Notebook.git
```

This allows pulling future upstream improvements into the client repo.

### 3.3 Update client-specific files

Files to customize for the new client:
- `seed/nesvick-podcast-templates.surql` — replace with client's speaker/episode profiles
- `seed/import-profiles.sh` — update profile names, voice IDs, briefing text
- `seed/update-tts-config.sh` — update TTS settings for client's voices
- Access code in env vars (`APP_ACCESS_CODE`)

### 3.4 Files that must NEVER be changed during syncs

When pulling from `stable-notebook` upstream in the future, always protect:
```
api/clerk_auth.py
api/routers/clerk_auth.py
frontend/src/middleware.ts
frontend/src/app/(auth)/
frontend/src/app/access-code/
frontend/src/components/auth/
SYNC.md
seed/<client>-podcast-templates.surql
```

---

## 4. Phase 2 — VPS Provisioning

### 4.1 Choose a VPS provider

**Recommended spec (minimum):**
- 2 vCPU
- 4GB RAM (8GB preferred)
- 40GB NVMe SSD
- Ubuntu 24.04 LTS

**Providers tested:** Hostinger KVM 2 ($8.99/mo) — confirmed working.

### 4.2 Create the VPS

**⚠️ CRITICAL: Choose "plain OS" — NOT a Docker image or control panel image.**

On Hostinger:
1. Go to VPS → Order → Select plan
2. OS: **Ubuntu 24.04** (from the OS tab, not Docker/apps tab)
3. Complete purchase, wait for provisioning (~2 min)
4. Note the VPS IP address

### 4.3 Initial SSH access

```bash
ssh root@<VPS_IP>
# Enter root password from provider dashboard
```

Optionally add your SSH key for passwordless access:
```bash
ssh-copy-id root@<VPS_IP>
```

### 4.4 Basic server prep (optional but recommended)

```bash
apt update && apt upgrade -y
timedatectl set-timezone America/Chicago  # or your timezone
```

---

## 5. Phase 3 — Coolify Installation

### 5.1 Install Coolify

```bash
ssh root@<VPS_IP>
curl -fsSL https://cdn.coollabs.io/coolify/install.sh | bash
```

Wait ~3-5 minutes. When done:
- Coolify UI: `http://<VPS_IP>:8000`
- Traefik proxy starts automatically on ports 80 and 443

### 5.2 Create admin account

Navigate to `http://<VPS_IP>:8000` → complete registration with email + password.

### 5.3 Complete the onboarding wizard

Coolify will prompt you through:
1. Register/login ✓
2. Server selection — **choose "localhost"** (see note below)
3. Create first project

> **⚠️ CRITICAL GOTCHA — Localhost vs Remote Server:**
> When Coolify asks to select a server, choose **"localhost"** (the option that says it's the server Coolify is running on). Do NOT add the VPS IP as a "remote server" — that creates a separate SSH connection Coolify uses to manage OTHER machines. For a self-hosted single VPS, `localhost` is always the right choice.

### 5.4 Verify server health

Coolify → Servers → localhost → should show green/healthy.

---

## 6. Phase 4 — DNS Setup

DNS must be configured BEFORE deploying, so Traefik can issue the SSL certificate.

### 6.1 Add the app A record

In your DNS provider, add:
| Type | Name | Value | TTL |
|------|------|-------|-----|
| A | `<subdomain>` | `<VPS_IP>` | 3600 |

Example: Name `nesvick.podcast` → IP `187.124.231.206` for domain `stablemischief.ai`

> **Finding your DNS provider:** The domain may be registered at GoDaddy but use different nameservers (e.g., Bluehost). Check: `whois yourdomain.com | grep "Name Server"` to find where DNS is actually managed.

### 6.2 Add the 5 Clerk CNAMEs

**⚠️ DO THIS BEFORE SETTING UP CLERK** — or do it immediately after creating the Clerk production project (step 7.4).

The CNAMEs are found in Clerk Dashboard → Production → Configure → Domains after adding your domain. The exact values are project-specific. For reference, the Nesvick CNAMEs were:

| Name | Type | Target |
|------|------|--------|
| `clerk.<subdomain>` | CNAME | `frontend-api.clerk.services` |
| `accounts.<subdomain>` | CNAME | `accounts.clerk.services` |
| `clkmail.<subdomain>` | CNAME | `mail.e667bmzqse08.clerk.services` |
| `clk._domainkey.<subdomain>` | CNAME | `dkim1.e667bmzqse08.clerk.services` |
| `clk2._domainkey.<subdomain>` | CNAME | `dkim2.e667bmzqse08.clerk.services` |

> The `e667bmzqse08` part is unique to the Clerk project — your values will differ. Always copy from Clerk Dashboard, never from this document.

### 6.3 Verify DNS propagation

```bash
nslookup <your-full-domain>
# Should return your VPS IP

dig CNAME clerk.<your-subdomain>.<domain>
# Should return frontend-api.clerk.services.
```

DNS typically propagates in minutes on Bluehost/Cloudflare. GoDaddy nameservers can take up to an hour.

---

## 7. Phase 5 — Clerk Production Setup

### 7.1 Create a new Clerk application

1. Go to [clerk.com](https://clerk.com) → Create application
2. Name: `<ClientName>-Podcast` (or similar)
3. Enable: **Google** (sign-in method)
4. Disable: Email/password (for cleaner UX, unless needed)

### 7.2 Switch to Production

In Clerk Dashboard, click **"Switch to Production"** in the top banner.

> **⚠️ CRITICAL:** Dev keys (`pk_test_`) are BLOCKED on real domains by Clerk. They only work on `localhost`. You MUST use production keys (`pk_live_`) for any deployed environment. This is non-negotiable — using dev keys on a real domain will result in Clerk refusing to load, causing a black screen.

### 7.3 Configure session token

Clerk Dashboard → Sessions → Token customization → Add:
```json
{
  "metadata": "{{user.public_metadata}}"
}
```

This is required for the access code gate to work. Without this, the `app_unlocked` metadata flag can't be read by the middleware.

### 7.4 Add your domain

Clerk Dashboard → Configure → Domains → Add domain → enter your full subdomain (e.g., `nesvick.podcast.stablemischief.ai`)

This shows you the 5 CNAME records needed (see Phase 4 step 6.2). Add them to DNS now if not already done.

After DNS propagates, click **"Verify configuration"** in Clerk. All 5 must show verified.

### 7.5 Copy production API keys

Clerk Dashboard → API Keys → copy:
- `CLERK_PUBLISHABLE_KEY` (starts with `pk_live_`)
- `CLERK_SECRET_KEY` (starts with `sk_live_`)

Store these safely — you'll need them in Phase 8.

---

## 8. Phase 6 — Google OAuth Setup

> Clerk's shared Google OAuth credentials will work for development but are NOT suitable for production (they show Clerk branding in the OAuth consent screen). You need a custom GCP project for a professional experience.

### 8.1 Create a GCP project

1. Go to [console.cloud.google.com](https://console.cloud.google.com)
2. Create new project (e.g., `CLIENTNAME`)
3. APIs & Services → OAuth consent screen → External → fill in app name, logo, etc.

### 8.2 Create OAuth credentials

APIs & Services → Credentials → Create credentials → OAuth client ID
- Application type: **Web application**
- Authorized redirect URIs → Add: `https://clerk.<your-domain>/v1/oauth_callback`

Example: `https://clerk.nesvick.podcast.stablemischief.ai/v1/oauth_callback`

> **⚠️ The redirect URI format is exact.** It must be `https://clerk.<your-full-subdomain>/v1/oauth_callback` — this is Clerk's generated callback URL for your production instance.

Copy the **Client ID** and **Client Secret**.

### 8.3 Connect to Clerk

Clerk Dashboard → Configure → Social connections → Google → Enable → paste Client ID + Client Secret → Save.

---

## 9. Phase 7 — Coolify Application Setup

### 9.1 Create a GitHub App in Coolify

Coolify → Sources → Add → GitHub App
- Name it something descriptive (e.g., `<client>-github`)
- Authorize for the GitHub org
- Grant access to the client repo

### 9.2 Create project + environment

Coolify → Projects → New Project → name it (e.g., `<ClientName>-Podcast`)
Then: + New Resource → inside the project → create `production` environment

### 9.3 Create the Docker Compose stack

+ New Resource → Docker Compose (Flexible)
- Source: GitHub App (created above)
- Repository: the client repo
- Branch: `main`
- Build Pack: **Docker Compose**
- Base Directory: `/`
- Docker Compose Location: `/docker-compose.yml`

### 9.4 Set the domain in Coolify

Stack → Domains → add `https://<your-full-domain>`

Coolify will automatically configure Traefik to route traffic and issue a Let's Encrypt SSL cert.

### 9.5 Set Traefik port routing

> This was a hard-to-debug issue the first time. The docker-compose has two ports (8502 for frontend, 5055 for API). Traefik needs to know which port to route to.

In `docker-compose.yml`, the `open_notebook` service should have:
```yaml
labels:
  - "traefik.http.services.open_notebook.loadbalancer.server.port=8502"
```

Coolify may add Traefik labels automatically from the domain you set. Verify by checking:
```bash
docker inspect <container_name> | grep traefik
```
Port `8502` must be the target — not `5055`.

---

## 10. Phase 8 — Environment Variables

This is the most critical phase. Getting env vars wrong causes subtle failures that are hard to diagnose.

### 10.1 Understanding buildtime vs runtime

In Next.js, `NEXT_PUBLIC_*` variables are baked into the JavaScript bundle at **build time**. Setting them as runtime-only env vars in Coolify WILL NOT work — they must be marked as **both buildtime and runtime**.

All other vars are runtime only.

### 10.2 Set buildtime + runtime variables

In Coolify → Stack → Environment Variables, mark these as **Build + Runtime**:

```
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=pk_live_<your_key>
NEXT_PUBLIC_API_URL=https://<your-full-domain>
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in
```

### 10.3 Set runtime-only variables

```
CLERK_PUBLISHABLE_KEY=pk_live_<your_key>       # must match NEXT_PUBLIC version exactly
CLERK_SECRET_KEY=sk_live_<your_key>
APP_ACCESS_CODE=<your_access_code>
OPEN_NOTEBOOK_ENCRYPTION_KEY=<generated_key>
SURREAL_URL=ws://surrealdb:8000/rpc
SURREAL_USER=root
SURREAL_PASSWORD=<strong_password>             # NOT root
SURREAL_NAMESPACE=open_notebook
SURREAL_DATABASE=open_notebook
ANTHROPIC_API_KEY=<your_key>
ELEVENLABS_API_KEY=<your_key>
OPENAI_API_KEY=<your_key>
```

### 10.4 Variables that must NOT be set

> **⚠️ CRITICAL BUG — Do NOT set `API_URL`**

The `docker-compose.yml` previously contained `API_URL=http://127.0.0.1:5055`. This was fixed in PR #21 and removed. Do NOT add it back in Coolify either.

**Why:** The frontend's `/config` route handler reads `API_URL` and returns it to the browser as the base URL for all API calls. `http://127.0.0.1:5055` is the server's localhost — from the browser's perspective this is the user's own machine. Safari blocks this as mixed content (HTTP on HTTPS). Chrome is more lenient but it still breaks audio playback. Never set this.

**How the API is called instead:** The browser calls relative paths like `/api/config`, which the Next.js server proxies to `localhost:5055` internally via rewrites in `next.config.ts`. This never involves the browser hitting the API directly.

### 10.5 Verify CLERK_PUBLISHABLE_KEY consistency

`CLERK_PUBLISHABLE_KEY` and `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` must be **identical values**. This was a bug in the initial deployment. Mismatched keys cause Clerk to fail silently on production domains.

---

## 11. Phase 9 — First Deploy

### 11.1 Trigger the deploy

Coolify → Stack → **Deploy** button

Watch the build logs. A full build takes 4-8 minutes (Next.js compilation + Python dependencies).

### 11.2 What to watch for in logs

Successful startup sequence looks like:
```
supervisord started with pid 1
spawned: 'surrealdb' with pid X
spawned: 'api' with pid X
spawned: 'worker' with pid X
spawned: 'frontend' with pid X
Waiting for API to be ready at http://localhost:5055/health...
[API logs showing startup]
INFO: Uvicorn running on http://0.0.0.0:5055
API is ready! Starting frontend...
▲ Next.js X.X.X
- Local: http://localhost:8502
✓ Ready in XXms
```

### 11.3 If the build fails

Common failure modes:
- **npm install fails** — check for Node version compatibility in Dockerfile
- **Python dependency fails** — check `pyproject.toml` for conflicts
- **Next.js build fails** — almost always a missing `NEXT_PUBLIC_*` env var that's required at build time
- **SurrealDB won't start** — check volume mount permissions

### 11.4 Verify containers are running

```bash
ssh root@<VPS_IP>
docker ps | grep -E "open_notebook|surrealdb"
```

Should show 2 containers running.

### 11.5 If the build used cached images

> **⚠️ Docker Caching Gotcha:** If code changes aren't showing up, Coolify may have used a cached Docker layer. Use **Force Rebuild** (no-cache option) in Coolify to force a complete fresh build. This is especially important after:
> - Changing any `NEXT_PUBLIC_*` env var
> - Merging code fixes to `main`
> - Suspecting stale behavior after deploy

---

## 12. Phase 10 — Post-Deploy Seeding

After the app is running, seed the client's speaker and episode profiles.

### 12.1 Verify API is reachable

```bash
curl https://<your-domain>/api/config
# Should return: {"version":"...","dbStatus":"online"}
```

### 12.2 Run seed scripts

The seed scripts need to point to the production URL. Update `BASE` in each script or run inline:

```bash
cd /path/to/repo/seed

# Seed speaker + episode profiles
BASE=https://<your-domain> bash import-profiles.sh

# Set TTS voice config
BASE=https://<your-domain> bash update-tts-config.sh
```

> **Note:** The seed scripts call the API at port 5055 if `BASE` uses the raw IP+port. For production, always use the domain URL so requests go through Traefik → Next.js proxy → FastAPI. Direct port 5055 access (via `http://<VPS_IP>:5055`) also works if the port is exposed, but should not be used in production.

### 12.3 Verify seeding via API

```bash
curl https://<your-domain>/api/speaker-profiles | python3 -m json.tool
curl https://<your-domain>/api/episode-profiles | python3 -m json.tool
```

Confirm the expected profiles appear.

---

## 13. Phase 11 — Smoke Tests

Test the full user journey before handing off to a client.

### Authentication Flow
- [ ] Navigate to `https://<your-domain>` → redirects to `/sign-in` ✓
- [ ] Redirect URL in the sign-in URL is the correct public domain (NOT `0.0.0.0:8502`) ✓
- [ ] Google Sign-in works → redirects to `/access-code` ✓
- [ ] Enter access code → redirects to `/notebooks` ✓
- [ ] Second visit: already logged in, lands on `/notebooks` ✓

### App Functionality
- [ ] Notebooks page loads ✓
- [ ] Browser console shows NO mixed content errors ✓
- [ ] Speaker profiles appear (Zachary + Riley or client equivalents) ✓
- [ ] Episode profiles appear ✓
- [ ] Can create a notebook ✓
- [ ] Can add a source (paste article text) ✓
- [ ] Podcast generation runs end-to-end ✓
- [ ] Generated audio plays in-app ✓
- [ ] Audio plays on mobile (Safari + Chrome) ✓

### Cross-Device Check
- [ ] Test on desktop Chrome ✓
- [ ] Test on iPhone Safari ✓
- [ ] Test on iPad ✓

---

## 14. Troubleshooting Guide

### "Load failed" / "Unable to connect to API" on mobile (Safari)

**Cause:** `API_URL=http://127.0.0.1:5055` is set somewhere (Coolify env vars or docker-compose.yml). The `/config` route returns this to the browser, which tries to call its own localhost. Safari blocks HTTP on HTTPS pages.

**Fix:** Remove `API_URL` from all env var sources. The Next.js rewrite proxy handles this internally.

**Diagnosis:** Open browser devtools → console → look for "Mixed Content" errors showing `http://127.0.0.1:5055`.

---

### Black screen after sign-in / Clerk loads but crashes

**Cause 1 — DNS not verified:** The 5 Clerk CNAME records haven't propagated yet.

**Diagnosis:** Clerk Dashboard → Domains → check that all 5 are verified. If not, wait for DNS propagation.

**Cause 2 — Wrong redirect URL:** The sign-in URL contains `redirect_url=https://0.0.0.0:8502/` (internal container address).

**Diagnosis:** Check the full sign-in URL in the browser. If it contains `0.0.0.0`, the middleware fix from PR #20 is not deployed.

**Fix:** Ensure `fix/middleware-public-url` (PR #20) is merged and a Force Rebuild was done in Coolify.

**Cause 3 — Dev Clerk keys on production domain:** Using `pk_test_` keys on any real domain.

**Fix:** Must use `pk_live_` keys from the Clerk production environment.

---

### Sign-in redirect URL shows `0.0.0.0:8502`

**Cause:** The `middleware.ts` is using `req.url` to build the redirect URL. Inside Docker behind Traefik, `req.url` resolves to the internal container address.

**Fix (PR #20):** `middleware.ts` must use `x-forwarded-host` and `x-forwarded-proto` headers from Traefik to reconstruct the public URL:

```typescript
function getPublicUrl(req: NextRequest): string {
  const forwardedHost = req.headers.get('x-forwarded-host') || req.nextUrl.host
  const forwardedProto = req.headers.get('x-forwarded-proto') || req.nextUrl.protocol.replace(':', '')
  return `${forwardedProto}://${forwardedHost}${req.nextUrl.pathname}${req.nextUrl.search}`
}
```

Then use `getPublicUrl(req)` everywhere `req.url` was used for redirects.

---

### App works on Chrome laptop but fails on Safari / iPhone

**Root cause:** Chrome is more lenient about mixed content (HTTP requests from HTTPS pages). Safari is strict.

**Always check for `API_URL` being set** — see "Load failed" entry above.

---

### API changes not taking effect after deploy

**Cause:** Docker is using a cached image layer.

**Fix:** In Coolify → use **Force Rebuild** (no-cache). This ignores all cached layers and builds completely fresh. Required any time:
- `NEXT_PUBLIC_*` env vars change (they're baked at build time)
- Code changes aren't showing up
- You're not sure if the latest code is deployed

---

### Traefik routing to wrong port (API instead of frontend)

**Cause:** When `docker-compose.yml` exposes multiple ports, Traefik may pick the wrong one.

**Diagnosis:**
```bash
docker inspect <container> | grep -A2 "traefik.*loadbalancer"
```
Should show port `8502`. If it shows `5055`, the Traefik label is wrong.

**Fix:** Ensure this label exists in `docker-compose.yml` under the `open_notebook` service:
```yaml
labels:
  - "traefik.http.services.open_notebook.loadbalancer.server.port=8502"
```

---

### SurrealDB silently drops custom fields on SCHEMAFULL tables

**Cause:** SurrealDB SCHEMAFULL tables drop any fields not declared in the schema. `TYPE object` is NOT sufficient for nested objects — sub-fields must also be declared OR the parent field must use `FLEXIBLE TYPE object`.

**Example:** `tts_config` field on `speaker_profile` table was silently dropped on every write until a migration was added declaring it as `FLEXIBLE TYPE option<object>`.

**Fix pattern:**
```sql
DEFINE FIELD OVERWRITE tts_config ON TABLE speaker_profile FLEXIBLE TYPE option<object>;
DEFINE FIELD OVERWRITE speakers[*] ON TABLE speaker_profile FLEXIBLE TYPE object;
DEFINE FIELD OVERWRITE speakers[*].tts_config ON TABLE speaker_profile FLEXIBLE TYPE option<object>;
```

The `speakers[*]` parent field ALSO needs to be `FLEXIBLE TYPE object` — declaring only the sub-field is not sufficient if the parent is non-FLEXIBLE.

---

### Coolify SSH key validation fails

**Cause 1:** The server IP field in Coolify reset to `localhost` silently before you clicked Validate.

**Fix:** Double-check the IP is saved in the server config field. Coolify has a known issue where it resets the field. Re-enter and verify.

**Cause 2 (self-hosted VPS):** Don't add the VPS as a "remote server" at all. Use `localhost` — that's the right choice when Coolify is running on the same VPS it's managing.

---

### `NEXT_PUBLIC_*` env var not working at runtime

**Cause:** The variable was set as runtime-only in Coolify, but it needs to be baked into the JavaScript bundle at build time.

**Fix:** In Coolify → Environment Variables → find the var → enable **"Also as Build Variable"** (buildtime + runtime). Then Force Rebuild.

---

### Coolify auto-deploy not triggering on push to main

**Check:**
1. Coolify → Stack → Settings → Auto-deploy is enabled
2. The GitHub App has webhook permissions
3. The branch name matches exactly (`main` not `master`)

---

## 15. Ongoing Maintenance

### Pulling upstream updates from Stable-Notebook

```bash
cd /path/to/client-repo
git fetch stable-notebook
git merge stable-notebook/main --no-ff

# Review changes carefully — protect client-specific files:
# api/clerk_auth.py, api/routers/clerk_auth.py
# frontend/src/middleware.ts
# frontend/src/app/(auth)/
# frontend/src/app/access-code/
# seed/<client>-podcast-templates.surql

git push origin main
# Coolify auto-deploys
```

### Adding new AI models

Both files must be updated (they're separate static lists):
1. `open_notebook/ai/model_discovery.py` → `ANTHROPIC_MODELS` dict
2. `api/credentials_service.py` → `STATIC_MODELS` dict inside `discover_with_config()`

### SurrealDB migrations

Migrations run automatically on startup. To add a new migration:
1. Create `open_notebook/database/migrations/<N>.surrealql`
2. Register it in `open_notebook/database/async_migrate.py`
3. Always increment N from the current latest

Check current migration version:
```bash
docker exec <container> python3 -c "
import asyncio
from open_notebook.database.async_migrate import get_current_version
print(asyncio.run(get_current_version()))
"
```

### Backing up SurrealDB data

```bash
# SSH into VPS
ssh root@<VPS_IP>

# Find SurrealDB container
docker ps | grep surrealdb

# Export data
docker exec <surrealdb_container> surreal export \
  --conn http://localhost:8000 \
  --user root --pass <password> \
  --ns open_notebook --db open_notebook \
  /tmp/backup.surql

docker cp <surrealdb_container>:/tmp/backup.surql ./backup-$(date +%Y%m%d).surql
```

### Monitoring the app

```bash
# Watch live logs
ssh root@<VPS_IP>
docker logs -f <open_notebook_container>

# Check both containers running
docker ps | grep -E "open_notebook|surrealdb"

# Test API health
curl https://<your-domain>/api/health
```

---

## Appendix A — Stable-Notebook Backport Checklist

> These are changes discovered/made during the Nesvick deployment that should be ported back to `Stable-Mischief-AI/Stable-Notebook` so it has feature parity. Stable-Notebook is the "parent" — it should contain everything except Nesvick-specific personalization (Clerk auth, custom profiles, access code).

### A.1 — Critical Bug Fix: Remove `API_URL` from docker-compose.yml
**Status:** Fixed in Nesvick PR #21. Needs to be applied to Stable-Notebook.

**Why:** `API_URL=http://127.0.0.1:5055` in docker-compose causes the `/config` route to return an internal server address to the browser. This breaks the app on Safari and causes mixed content errors on any HTTPS deployment.

**File:** `docker-compose.yml`  
**Change:** Remove the line `- API_URL=http://127.0.0.1:5055` from the `open_notebook` environment section.

---

### A.2 — Verify: Next.js Rewrite Proxy in `next.config.ts`
**Status:** Should already exist in Stable-Notebook (it was present in Nesvick and Nesvick derived from it).

**What to verify:** `next.config.ts` has the `/api/*` rewrite block:
```typescript
async rewrites() {
  const internalApiUrl = process.env.INTERNAL_API_URL || 'http://localhost:5055'
  return [{
    source: '/api/:path*',
    destination: `${internalApiUrl}/api/:path*`,
  }]
}
```

This is the correct architecture — browsers never call port 5055 directly; they use the Next.js proxy.

---

### A.3 — Verify: Runtime Config Route `/config/route.ts`
**Status:** Should already exist in Stable-Notebook.

**What to verify:** `frontend/src/app/config/route.ts` exists and reads `API_URL || NEXT_PUBLIC_API_URL` to return the browser's API base URL.

**Note:** With A.1 fixed (removing `API_URL`), this route correctly falls back to `NEXT_PUBLIC_API_URL` (the public HTTPS domain), which works correctly.

---

### A.4 — Feature: TTS Config (`tts_config`) on SpeakerProfile
**Status:** Added in Nesvick PRs #16-#19. NOT yet in Stable-Notebook.

**Why this belongs upstream:** Any deployment of Stable-Notebook that uses ElevenLabs will want per-speaker TTS configuration (stability, style, speed, similarity_boost). Without this, voice settings can't be persisted.

**Changes needed (4 parts):**
1. **`open_notebook/podcasts/models.py`** — Add `tts_config: Optional[Dict[str, Any]] = None` to `SpeakerProfile`, `SpeakerProfileCreate`, and `SpeakerProfileResponse`
2. **`api/routers/speaker_profiles.py`** — Include `tts_config` in all CRUD operations
3. **`open_notebook/database/migrations/16.surrealql`** — Define schema fields with `FLEXIBLE TYPE`
4. **`open_notebook/database/async_migrate.py`** — Register migration 16

Full migration SQL (use EXACTLY this — was validated on SurrealDB 3.0.4):
```sql
DEFINE FIELD OVERWRITE tts_config ON TABLE speaker_profile FLEXIBLE TYPE option<object>;
DEFINE FIELD OVERWRITE speakers[*] ON TABLE speaker_profile FLEXIBLE TYPE object;
DEFINE FIELD OVERWRITE speakers[*].tts_config ON TABLE speaker_profile FLEXIBLE TYPE option<object>;
```

**Key lesson:** `speakers[*]` (the parent) MUST be `FLEXIBLE TYPE object`. Declaring only `speakers[*].tts_config` is insufficient — the non-FLEXIBLE parent drops all sub-fields not explicitly declared.

---

### A.5 — Model Updates: Latest Anthropic Models
**Status:** Already applied to Stable-Notebook (PR #14 was done there first).

**Current model list in both repos:**
- `claude-opus-4-6` (latest)
- `claude-sonnet-4-6` (latest, recommended for podcast generation)
- `claude-haiku-4-5-20251001`
- `claude-opus-4-20250514`
- `claude-sonnet-4-20250514`
- `claude-3-5-sonnet-20241022`
- `claude-3-5-haiku-20241022`

**Two files must be kept in sync:**
1. `open_notebook/ai/model_discovery.py` → `ANTHROPIC_MODELS`
2. `api/credentials_service.py` → `STATIC_MODELS` in `discover_with_config()`

---

### A.6 — Model Updates: ElevenLabs `eleven_v3`
**Status:** Added in Nesvick PR #15. Check if in Stable-Notebook.

**What to verify:** ElevenLabs model list in the relevant discovery/config file includes `eleven_v3` (the latest, highest-quality model — noticeable improvement over `eleven_turbo_v2_5`).

---

### A.7 — Verify: Dockerfile.single + supervisord config
**Status:** Should already exist — Nesvick derived its build from Stable-Notebook.

**What to verify:**
- `Dockerfile.single` exists and builds both frontend + backend in a single image
- `supervisord.single.conf` starts: surrealdb, api, worker, frontend (in that order)
- `scripts/wait-for-api.sh` exists and the frontend supervisor entry waits for API health

**Critical detail in supervisord.single.conf:**
```ini
[program:frontend]
command=bash -c "/app/scripts/wait-for-api.sh && node server.js"
directory=/app/frontend
environment=NODE_ENV="production",PORT="8502"
passenv=API_URL,NEXT_PUBLIC_API_URL,INTERNAL_API_URL
```

The `passenv` directive passes specific env vars to the Next.js process. With `API_URL` removed (see A.1), these three are correct.

---

### A.8 — Documentation: SYNC.md Pattern
**Status:** Exists in Nesvick only.

**Recommendation:** Add a `DEPLOYMENT.md` or similar to Stable-Notebook documenting:
- The correct env var setup for production deployment
- The `API_URL` gotcha (don't set it)
- The buildtime vs runtime distinction for `NEXT_PUBLIC_*` vars
- The Traefik port routing requirement

---

### A.9 — Hardening: docker-compose.yml SurrealDB credentials
**Status:** Not yet fixed in either repo.

Current `docker-compose.yml` has `SURREAL_USER=root` and `SURREAL_PASSWORD=root` hardcoded. This should use env vars:
```yaml
environment:
  - SURREAL_USER=${SURREAL_USER:-root}
  - SURREAL_PASSWORD=${SURREAL_PASSWORD}
```

And in Coolify/`.env`, set a strong `SURREAL_PASSWORD`.

---

### A.10 — Security: Move port 5055 from `ports:` to `expose:`
**Status:** Not yet fixed.

---

### A.11 — Feature: Shareable Episode URL (Supabase-backed public player)
**Status:** Built in Nesvick PR #22. NOT in Stable-Notebook.

**What it does:** Adds a "Generate Shareable URL" button on completed episodes. Uploads audio to Supabase Storage, saves metadata to a `shared_episodes` table, returns a permanent public link at `/share/{id}` that opens a branded player page — no login required.

**Files changed:**
- `api/routers/share.py` — 3 new endpoints: POST create share, GET share status, GET public episode data
- `api/main.py` — register share router
- `frontend/src/app/share/[id]/page.tsx` — SSR public player page (no Clerk)
- `frontend/src/app/share/[id]/SharePlayerClient.tsx` — client component with audio controls
- `frontend/src/components/podcasts/EpisodeCard.tsx` — share button in advanced mode
- `frontend/src/components/simple-mode/SimpleEpisodeCard.tsx` — share button in simple mode
- `frontend/src/middleware.ts` — `/share/(.*)` added to public routes
- `frontend/src/lib/api/podcasts.ts` — share API calls
- `frontend/src/lib/hooks/use-podcasts.ts` — `useShareEpisode`, `useEpisodeShareStatus` hooks
- `frontend/src/lib/types/podcasts.ts` — share response types
- `pyproject.toml` + `uv.lock` — adds `supabase` Python package
- `tests/test_share.py` — unit tests

**New env vars required:**
```
SUPABASE_URL=https://<project-ref>.supabase.co
SUPABASE_SERVICE_ROLE_KEY=<service role JWT>
SUPABASE_ANON_KEY=<anon JWT>
NEXT_PUBLIC_SUPABASE_URL=https://<project-ref>.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=<anon JWT>
FRONTEND_PUBLIC_URL=https://<your-domain>   # used to build share URLs
```

**Supabase pre-setup SQL:**
```sql
create table public.shared_episodes (
  id            text primary key,
  episode_id    text not null,
  title         text not null,
  audio_url     text not null,
  transcript    text,
  source_text   text,
  briefing      text,
  key_topics    jsonb default '[]'::jsonb,
  host_names    jsonb default '[]'::jsonb,
  duration_sec  integer,
  share_url     text not null,
  created_at    timestamptz default now()
);
alter table public.shared_episodes enable row level security;
create policy "Public read access" on public.shared_episodes for select using (true);
```

Also create a Storage bucket named `podcast-episodes` set to **public**.

**Note for Stable-Notebook backport:** The `/share/[id]` player page is intentionally unstyled (no NTG branding). When backporting, use a generic branded design. The backend share.py is fully reusable.

---

### A.12 — Feature: Disable Version Update Notification
**Status:** Fixed in Nesvick PR #23. Should also be applied to any private client deployment (not necessarily Stable-Notebook itself).

**What it does:** Makes `frontend/src/lib/hooks/use-version-check.ts` a no-op, preventing upstream GitHub update notifications from appearing in client deployments.

**Note:** This is appropriate for private/client deployments only. Stable-Notebook itself should keep the version check active.

---

### A.13 — Fix: Simple Mode Transcript Save Not Reflecting
**Status:** Fixed in Nesvick `task-feat-shareable-episode-url` branch. Pre-existing bug.

**Root cause:** `useUpdateEpisodeTranscript` only invalidated `QUERY_KEYS.podcastEpisodes` on success, but Simple Mode uses a separate `basicModeEpisode` query cache. After saving, the old transcript was returned from cache.

**Fix:** In `frontend/src/lib/hooks/use-podcasts.ts`, add to `onSuccess` of `useUpdateEpisodeTranscript`, `useApproveTranscript`, and `useRetryTranscript`:
```typescript
void queryClient.invalidateQueries({ queryKey: ["basic-mode", "episode"] })
```

---

### A.14 — Feature: TTS Text Normalizer Pre-processor
**Status:** Built in Nesvick branch `feat/tts-text-normalizer`. NOT in Stable-Notebook.

**What it does:** Intercepts dialogue text before it is sent to ElevenLabs and normalizes it to prevent TTS artifacts (foreign language glitches caused by em dashes, financial shorthand, spread notation, special characters).

**Root cause discovered:** ElevenLabs Turbo v2.5 interprets em dashes (—) mid-sentence as a prosody/language switch cue, causing audible foreign language artifacts.

**Files:**
- `api/tts_normalizer.py` — `normalize_tts_text(text)` function
- `commands/podcast_commands.py` — calls normalizer before ElevenLabs on initial generation
- `commands/regenerate_audio_command.py` — calls normalizer before ElevenLabs on regeneration
- `tests/test_tts_normalizer.py` — unit tests

**Normalizations applied:**
- Em dash / en dash → comma
- `$2.75` → `2 dollars and 75 cents`
- `May 14-15` → `May 14th through 15th`
- `SN-SX` → `SN to SX` (spread notation)
- `5%` → `5 percent`
- Special chars (`&`, `@`, `#`, `*`) → space

---

### A.15 — Stability Settings: Zachary Davis Voice Tuning
**Status:** Applied to Nesvick VPS via API. NOT in Stable-Notebook (Stable-Notebook uses different voices).

**Recommended TTS config for commodity/financial podcast content:**
- Profile-level (Zachary): `stability: 0.55, similarity_boost: 0.80, style: 0.70, speed: 1.15`
- Riley per-speaker override: `stability: 0.35, similarity_boost: 0.80, style: 0.80, speed: 1.30`

**Why stability 0.55 for Zachary:** Lower stability (0.35) produced occasional foreign language artifacts on financial shorthand even with the normalizer active. 0.55 is the stability floor that eliminates artifacts while keeping natural delivery.

---

### A.16 — Episode Briefing: Spoken Audio Formatting Rules
**Status:** Applied to Nesvick VPS episode profile. Should be added to any podcast episode profile that feeds ElevenLabs.

**Add this block to the end of any episode `default_briefing`:**
```
CRITICAL - SPOKEN AUDIO FORMATTING: This transcript goes to a TTS engine.
NEVER use em dashes or en dashes - replace with a comma.
Never use hyphens in spread notation (write 'SN to SX' not 'SN-SX').
Write currency as words (two dollars and seventy-five cents, not $2.75).
Write percent as words (five percent, not 5%).
Write date ranges as words (May 14th through 15th, not May 14-15).
No special characters: no slashes, ampersands, or hash symbols.
Violating these rules causes foreign language glitches in the audio.
```

---

### A.10 — Security: Move port 5055 from `ports:` to `expose:`
**Status:** Not yet fixed.

Currently `docker-compose.yml` exposes port 5055 publicly:
```yaml
ports:
  - "8502:8502"
  - "5055:5055"  # ← should be expose:, not ports:
```

Port 5055 should only be accessible within the Docker network (Next.js → FastAPI), not from the public internet. Change to:
```yaml
ports:
  - "8502:8502"
expose:
  - "5055"
```

Note: Traefik uses Docker's internal network to route to containers, so moving 5055 to `expose:` does NOT break Traefik routing to port 8502.

---

## Appendix B — Critical Files Reference

### Files in client repo that are Nesvick-specific (never sync to Stable-Notebook)
```
api/clerk_auth.py                        # Clerk JWT verification
api/routers/clerk_auth.py                # /api/auth/verify-access-code
frontend/src/middleware.ts               # Clerk middleware
frontend/src/app/(auth)/                 # Sign-in/sign-up pages
frontend/src/app/access-code/            # Access code entry page
frontend/src/components/auth/            # Auth UI components
SYNC.md                                  # Sync instructions
seed/nesvick-podcast-templates.surql     # Client-specific profiles
seed/import-profiles.sh                  # Seeding script
seed/update-tts-config.sh               # TTS config seeding
```

### Environment variables — complete reference

| Variable | Buildtime | Runtime | Notes |
|----------|-----------|---------|-------|
| `NEXT_PUBLIC_API_URL` | ✅ | ✅ | Must be `https://<domain>` |
| `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` | ✅ | ✅ | Must match `CLERK_PUBLISHABLE_KEY` exactly |
| `NEXT_PUBLIC_CLERK_SIGN_IN_URL` | ✅ | ✅ | Always `/sign-in` |
| `CLERK_PUBLISHABLE_KEY` | — | ✅ | `pk_live_...` |
| `CLERK_SECRET_KEY` | — | ✅ | `sk_live_...` |
| `APP_ACCESS_CODE` | — | ✅ | Numeric gate code |
| `OPEN_NOTEBOOK_ENCRYPTION_KEY` | — | ✅ | Fernet key, generate fresh per deployment |
| `SURREAL_URL` | — | ✅ | Always `ws://surrealdb:8000/rpc` |
| `SURREAL_USER` | — | ✅ | |
| `SURREAL_PASSWORD` | — | ✅ | Never `root` in production |
| `SURREAL_NAMESPACE` | — | ✅ | `open_notebook` |
| `SURREAL_DATABASE` | — | ✅ | `open_notebook` |
| `ANTHROPIC_API_KEY` | — | ✅ | |
| `ELEVENLABS_API_KEY` | — | ✅ | |
| `OPENAI_API_KEY` | — | ✅ | |
| `API_URL` | ❌ NEVER | ❌ NEVER | Removed in PR #21 — causes mixed content |
| `INTERNAL_API_URL` | — | optional | Defaults to `http://localhost:5055` — only set to override |

---

*Document maintained in: `projects/nesvick/deployment-playbook.md`*
*Source repo: `Stable-Mischief-AI/open-notebook-nesvick`*
