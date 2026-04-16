# Open Notebook / Stable Notebook (Product — Stable Mischief)

**Official name: Stable Notebook** (`Stable-Mischief-AI/Stable-Notebook` on GitHub). Fully independent — not a fork. Started from the `lfnovo/open-notebook` OSS project but completely detached. No upstream connection.

⚠️ **Repo extraction pending:** Currently lives at `/Users/jw-dev/Developer/GitHub/adw-orchestrator/apps/open-notebook` inside the no-longer-used adw-orchestrator monorepo. Needs to be extracted to its own local directory (removing the local install as part of the move).

## What It Is
Open-source, privacy-focused alternative to Google NotebookLM. AI-powered research + podcast platform. Users upload content (PDFs, audio, video, web pages), get intelligent notes, semantic search, AI chat, and professional podcast generation — fully self-hosted, multi-provider AI.

Stable Mischief's version adds: daily automated podcast generation from newsletter/commentary content, Nesvick-specific host personas, transcript review agent, strict turn count length control.

## Stack
- Frontend: React/Next.js (port 3000), Shadcn/ui + Tailwind
- API: FastAPI (port 5055), LangGraph workflow orchestration
- DB: SurrealDB (graph database, port 8000)
- AI: multi-provider via Esperanto (Anthropic, etc.)
- Deployment: Docker Compose + Coolify (VPS)

## Current Deployment
- **Nesvick instance:** daily 5-min podcast from NTG Morning Comments, hosts Zachary Davis + Riley Chen
- Live: https://nesvick.podcast.stablemischief.ai | VPS: `187.124.231.206`
- See [[entities/clients/nesvick]] for client detail

## Repo Situation ⚠️
- Current location: `/Users/jw-dev/Developer/GitHub/adw-orchestrator/apps/open-notebook`
- **adw-orchestrator is no longer used** — open-notebook needs to be extracted to its own repo
- GitHub: confirm target org/name before extracting (e.g., `Stable-Mischief-AI/stable-notebook`)

## Critical Technical Decisions (Do Not Revisit)
- **Turn count = the ONLY reliable length lever** — word count instructions are unreliable
- **enforce_transcript_length() must be deleted** — cuts from the end, destroys content
- Never edit upstream `podcast_creator` library files directly — override via our code

## Status
- Nesvick instance: production stable, all PRs merged ✅
- **Backport / generalization: pending** — apply Nesvick improvements back to base platform
- **Repo extraction: pending** — pull out of adw-orchestrator into standalone repo
- **Rename: pending** — away from "Open Notebook" to Stable-branded name

## Open Work
- [ ] Extract repo from `adw-orchestrator/apps/open-notebook` → standalone
- [ ] Rename to Stable Notebook (or chosen name)
- [ ] Backport Nesvick improvements to generalized platform
- [ ] Define client packaging model for additional podcast clients
