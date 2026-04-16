# AIBA — AI Business Analyst (Product — Stable Mischief)

## What It Is
Stable Mischief's first product. AI-powered system that transforms meeting transcripts into actionable business deliverables. Full name: **aiBA5** (AI Business Analyst 5).

Takes client meeting transcripts (20-50K tokens, processed whole — no chunking) and automatically generates:
- Process documentation (detailed markdown)
- Process diagrams (SVG flowcharts per sub-process)
- Opportunity analysis (15-20 automation/improvement opportunities with ROI estimates)
- Implementation roadmap (sequenced with timeline + resources)
- Client deliverables (PDF/PowerPoint via Presenton integration)

Key differentiator: extracts **specific actor names** from transcripts, not generic roles. Quantifies ROI with annual savings + build unit estimates.

## Status
- **ACTIVE** — client launches planned within weeks (as of 2026-04-16)

## Stack
- Backend: FastAPI + Python (uv), Alembic migrations
- Frontend: React/Next.js (port 3030), API at port 8001
- AI: Google Gemini (GEMINI_API_KEY) — full-context transcript analysis
- Deployment: Docker Compose
- Design Studio: interactive wizard for curating/customizing deliverables
- AI Infographics: Nano Banana Pro integration

## Repo
- Local: `/Users/jw-dev/Developer/GitHub/aiBA5`
- GitHub: `Stable-Mischief-AI/aiBA5` (private)

## Open Work
- [ ] Confirm target clients for upcoming launches
- [ ] Define packaging / pricing model
- [ ] Hosting/deployment setup for client instances
