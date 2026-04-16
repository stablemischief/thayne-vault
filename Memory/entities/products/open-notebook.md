# Open Notebook (Product — Stable Mischief)

## What It Is
AI-powered podcast generation platform. Takes a source document (e.g., newsletter, morning comments) and produces a daily multi-host spoken audio episode. First deployed for Nesvick Trading Group.

## Current Deployment
- **Nesvick instance:** `open-notebook-nesvick` — daily 5-min podcast from NTG Morning Comments. Two hosts: Zachary Davis + Riley Chen. Live: https://nesvick.podcast.stablemischief.ai
- See [[entities/clients/nesvick]] for Nesvick-specific detail.

## Product Roadmap
- **Backport phase upcoming:** Retroactively apply Nesvick-specific improvements (turn count control, transcript reviewer agent, sentence-opening conjunction rule) back to the generalized platform
- **Generalization goal:** Make improvements available to other clients wanting the same podcast-from-newsletter functionality
- **Target clients:** Organizations with regular written content (newsletters, reports, commentary) who want audio distribution

## Critical Technical Decisions (Do Not Revisit)
- **Turn count = the ONLY reliable length lever** — word count instructions in templates are unreliable
- **enforce_transcript_length() must be deleted** — cuts from the end, destroys content
- Never edit podcast_creator library files directly — override via client code

## Status
- Nesvick instance: all PRs merged, production stable ✅
- Generalization / backport: **pending** — upcoming work item

## Repo
- `open-notebook-nesvick` (GitHub) — confirm org before Archon dispatch
