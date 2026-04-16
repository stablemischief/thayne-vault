# Nesvick Podcast (open-notebook-nesvick)

## What It Is
Daily 5-minute spoken audio podcast from NTG Morning Comments newsletter.
Client: Nesvick Trading Group. Two hosts: Zachary Davis + Riley Chen.
Live: https://nesvick.podcast.stablemischief.ai

## Current State (as of 2026-04-06 — confirmed by James)
- **All PRs merged** ✅ — James confirmed all Nesvick repo PRs are merged as of 2026-04-06
- PR #22 (shareable episode URL): MERGED ✅
- PR #33 (transcript reviewer agent + simplified prompt): MERGED 2026-04-01
- PR #35 (force all segments to short turn count — 3 turns): MERGED 2026-04-01
- PR #36 (sentence-opening conjunction rule): MERGED ✅
- **TODO:** Switch Coolify to main, update seed script, backport to Stable-Notebook

## Critical Decisions (PROVEN — Do Not Revisit)
- **Turn count = the ONLY reliable length lever** — word count instructions in templates are unreliable
- **enforce_transcript_length() must be deleted** — it cuts from the end, destroys content
- Never edit podcast_creator library files directly — override via our code

## VPS
- IP: `187.124.231.206` | SSH: `root`
- Coolify UI: `http://187.124.231.206:8000`
