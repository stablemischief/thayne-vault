# Archon — Workflow Rules (NON-NEGOTIABLE)

> SOPs for Thayne orchestrating Archon. Archon codes, James merges.

## Workflow Selection — Use the RIGHT one every time

| Task | Workflow |
|------|----------|
| Research / validate only (no code) | `archon-assist` (no --branch) |
| Have a plan, need code written | `archon-plan-to-pr` |
| Natural language feature, no plan | `archon-idea-to-pr` |
| Fix a GitHub issue | `archon-fix-github-issue-dag` |
| Review a PR | `archon-smart-pr-review` |
| Full 5-agent PR review | `archon-comprehensive-pr-review` |
| Architecture/cleanup | `archon-architect` |
| PRD, short (≤7 stories) | `archon-ralph-stateful` |
| PRD, long (7+ stories) | `archon-ralph-fresh` |

**`archon-assist --branch` NEVER pushes to GitHub.** Using it for code = broken deploys.
**Root cause of 2026-03-29 production outage** — never repeat this.

## After Every Archon Run
Verify push landed: `git -C <worktree> log --oneline origin/<branch>..HEAD`
Must show 0 unpushed before telling James "ready to deploy."

## Archon Brief / Issue Quality Standard (MANDATORY)

Every GitHub issue sent to Archon must include all 8 sections:
1. **Problem Statement** — what fails/is missing, exact errors, file paths, function names
2. **Proposed Solution** — exact files to change, before/after, function signatures
3. **Approved Mockup** (UI issues) — FULL HTML embedded as code block, not a file path
4. **Code Examples** — concrete before/after snippets, type definitions, JSX/Python
5. **Key Files to Reference** — every file Archon should read before touching anything
6. **Acceptance Criteria** — numbered checklist, each item verifiable, tests pass before AND after
7. **What NOT to Change** — explicit list of files and behaviors that must remain untouched
8. **Research Findings** — facts Thayne observed: code snippets, line numbers, what was ruled out

**Show the complete issue to James and WAIT for explicit "go ahead" before dispatching Archon.**
