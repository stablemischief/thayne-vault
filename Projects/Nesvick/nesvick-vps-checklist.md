---
project: Nesvick
source: thane
imported: 2026-04-07
---
# Nesvick VPS Deployment Checklist
**Date:** 2026-03-20
**Domain:** nesvick.podcast.stablemischief.ai
**Starting point:** VPS built with Coolify image on Ubuntu, Coolify not yet configured

---

## VPS Details
- **IP:** `187.124.231.206`
- **User:** `root`
- **SSH Port:** `22`
- **OS:** Ubuntu 24.04.4 LTS
- **Coolify SSH Key:** `gentle-gannet-cp379sk7wpcr650g4strmmo9` (used for Nesvick-Podcast server)

## Coolify Server Setup — Lessons Learned
- When adding the server IP in Coolify, **confirm the field saved** before clicking Validate — Coolify sometimes resets the IP back to `localhost` without warning
- The working SSH key is `gentle-gannet` — its public key must be in `/root/.ssh/authorized_keys` on the VPS
- To add it: `docker exec coolify ssh-keygen -y -f /var/www/html/storage/app/ssh/keys/ssh_key@gentle-gannet-cp379sk7wpcr650g4strmmo9 >> /root/.ssh/authorized_keys`
- `localhost's key` cannot be used for remote servers — Coolify reserves it for localhost only
- SSH config is default Ubuntu — pubkey auth enabled, `PermitRootLogin yes`

---

## PHASE 1 — Coolify Initial Setup

- [ ] **1.1** Open Coolify UI in browser: `http://<VPS_IP>:8000`
- [ ] **1.2** Create root admin account (email + password)
- [ ] **1.3** Complete Coolify onboarding wizard
- [ ] **1.4** Coolify → Servers → confirm "localhost" server shows as healthy (green)

---

## PHASE 2 — DNS & Domain

- [ ] **2.1** Get your VPS IP from Hostinger panel
- [ ] **2.2** In Hostinger DNS (or wherever stablemischief.ai is managed) → add A record:
  - Name: `nesvick.podcast`
  - Points to: `<VPS IP>`
  - TTL: 3600
- [ ] **2.3** Wait for DNS propagation — verify with: `nslookup nesvick.podcast.stablemischief.ai`

---

## PHASE 3 — GitHub Connection

- [ ] **3.1** Coolify → Sources → Add → GitHub
- [ ] **3.2** Authorize Coolify app on GitHub
- [ ] **3.3** Grant access to `Stable-Mischief-AI/open-notebook-nesvick` repo

---

## PHASE 4 — Create Application in Coolify

- [ ] **4.1** Coolify → Projects → New Project → name it `nesvick`
- [ ] **4.2** New Resource → Application
- [ ] **4.3** Source: GitHub → `Stable-Mischief-AI/open-notebook-nesvick`
- [ ] **4.4** Branch: `main`
- [ ] **4.5** Build Pack: **Docker Compose**
- [ ] **4.6** Docker Compose file: `docker-compose.yml`

---

## PHASE 5 — Build Arguments (CRITICAL — do before first deploy)

These are baked into the Next.js build at compile time. Wrong value = broken app.

- [ ] **5.1** Coolify → Application → Build Arguments → add:
  ```
  NEXT_PUBLIC_API_URL=https://nesvick.podcast.stablemischief.ai
  ```

---

## PHASE 6 — Environment Variables

- [ ] **6.1** Coolify → Application → Environment Variables → add ALL of the following:

```
# Clerk Auth (use dev keys for initial test, switch to prod keys after smoke test)
CLERK_PUBLISHABLE_KEY=<from Clerk Dashboard>
CLERK_SECRET_KEY=<from Clerk Dashboard>
NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY=<same as CLERK_PUBLISHABLE_KEY>
NEXT_PUBLIC_CLERK_SIGN_IN_URL=/sign-in

# App
APP_ACCESS_CODE=38138
OPEN_NOTEBOOK_ENCRYPTION_KEY=<copy from MacBook Pro .env>

# SurrealDB (internal — do not expose port 8000 publicly)
SURREAL_URL=ws://surrealdb:8000/rpc
SURREAL_USER=root
SURREAL_PASSWORD=<set a strong password — NOT root:root>
SURREAL_NAMESPACE=open_notebook
SURREAL_DATABASE=open_notebook

# API URL (internal Docker network)
API_URL=http://127.0.0.1:5055

# AI Providers
ANTHROPIC_API_KEY=<your key>
ELEVENLABS_API_KEY=<your key>
OPENAI_API_KEY=<your key>
```

---

## PHASE 7 — Volume Mounts (CRITICAL — data persistence)

Without these, all data is lost on every restart.

- [ ] **7.1** Coolify → Application → Storages/Volumes → add:
  ```
  ./surreal_data  →  /mydata
  ./notebook_data →  /app/data
  ```

---

## PHASE 8 — Port & Domain Configuration

- [ ] **8.1** Coolify → Application → Domains → add: `nesvick.podcast.stablemischief.ai`
- [ ] **8.2** Enable Let's Encrypt SSL (auto)
- [ ] **8.3** Coolify → Application → Ports → confirm:
  - Port `8502` exposed publicly ✅
  - Port `5055` NOT exposed (internal only) ✅

---

## PHASE 9 — Clerk Dashboard

- [ ] **9.1** Go to Clerk Dashboard → nesvick-podcast project
- [ ] **9.2** Configure → Domains → add: `nesvick.podcast.stablemischief.ai`
- [ ] **9.3** Configure → Sessions → verify token customization still set: `{"metadata": "{{user.public_metadata}}"}`
- [ ] **9.4** Decide: use dev keys (`pk_test_`) for initial test, or switch to production keys now

---

## PHASE 10 — First Deploy

- [ ] **10.1** Coolify → Application → Deploy
- [ ] **10.2** Watch build logs — Next.js build takes 3-5 minutes
- [ ] **10.3** Verify all containers start (open_notebook + surrealdb)
- [ ] **10.4** Check logs for any startup errors

---

## PHASE 11 — Post-Deploy: Seed Profiles

- [ ] **11.1** SSH into VPS
- [ ] **11.2** Find the running container: `docker ps | grep open_notebook`
- [ ] **11.3** Run seed script to restore speaker + episode profiles:
  ```bash
  docker exec <container_name> bash seed/import-profiles.sh
  ```
  OR run against the live API:
  ```bash
  bash seed/import-profiles.sh  # (pointing at https://nesvick.podcast.stablemischief.ai)
  ```
- [ ] **11.4** Run TTS config script to set voice settings:
  ```bash
  bash seed/update-tts-config.sh
  ```
  (update BASE URL in script to point to VPS first)

---

## PHASE 12 — Smoke Tests

- [ ] Sign-in page loads at `https://nesvick.podcast.stablemischief.ai`
- [ ] Google OAuth sign-in works
- [ ] Access code `38138` accepted → redirects to notebooks
- [ ] Notebooks page loads (no 401 errors in browser console)
- [ ] Can create a notebook and add a source
- [ ] Podcast generation runs end-to-end (transcript + audio)
- [ ] Speaker profiles show Zachary + Riley with correct voice IDs
- [ ] Episode profile shows Nesvick Daily with correct briefing

---

## PHASE 13 — Switch to Production Clerk Keys (after smoke tests pass)

- [ ] **13.1** Clerk Dashboard → API Keys → copy Production keys
- [ ] **13.2** Coolify → Environment Variables → update:
  - `CLERK_PUBLISHABLE_KEY` → `pk_live_...`
  - `CLERK_SECRET_KEY` → `sk_live_...`
  - `NEXT_PUBLIC_CLERK_PUBLISHABLE_KEY` → `pk_live_...`
- [ ] **13.3** Redeploy
- [ ] **13.4** Re-test sign-in flow

---

## Notes
- `NEXT_PUBLIC_API_URL` is build-time — changing it requires a full redeploy
- SurrealDB volumes MUST be set before first deploy
- Port 5055 must NEVER be publicly exposed — Clerk middleware is the auth gate
- Supabase archive feature not yet built — podcast audio lives on VPS only for now
- After go-live, run `bash seed/update-tts-config.sh` with VPS URL to set Riley's voice settings
