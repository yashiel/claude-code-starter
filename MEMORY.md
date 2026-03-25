# Project Memory

> Cross-session context. Read this FIRST at every session start. Updated at every session end.
> This file is the bridge between sessions — without it, every session starts from zero.

## Latest Session

**Date**: 2026-03-25
**What was done**:
- Added Persona Protocol — all 8 roles run as structured checklist before any code
- Added Auto-Skill Loading matrix — skills auto-load based on task type, never skipped
- Added Memory System documentation — what gets remembered, when, where, how
- Updated plan skill to 8-lens with persona checklist
- Updated feature command to include persona protocol + memory updates
- Updated CLAUDE.md with Persona Protocol, Auto-Skill Loading, Memory System sections
- Rewrote MEMORY.md as proper template with clear structure

**Decisions made**:
- Personas run on EVERY task — not optional, not skippable
- Skills auto-load by task type — UI tasks always load all design skills
- Memory updates happen at session end AND on significant events (features completed, decisions made, bugs fixed)
- MEMORY.md structure: Latest Session → Architecture Decisions → Active Context → Session History

**Project state**:
- Scaffold only — no application code yet
- All docs in place: AGENTS, ARCHITECTURE, CONVENTIONS, SECURITY, PAYMENTS, security-playbook
- Custom skills: catchup, plan (8-lens), review
- Custom commands: catchup, commit, feature (persona-aware), fix, pr
- Branch: `main`
- Published: https://github.com/yashiel/claude-code-starter

**What's next**:
- User to define PROJECT_NAME and one-line description
- Initialize Next.js app (`npx create-next-app@latest`)
- Set up Appwrite project and configure env vars
- Install and configure shadcn/ui
- Begin feature development

## Architecture Decisions

| Decision | Why | Date |
|----------|-----|------|
| 8-role persona protocol on every task | Prevents tunnel vision — catches issues across system, code, DB, QA, design, UX, creative, frontend | 2026-03-22 |
| Dual-repo publishing (full + app-only) | Cloud providers only need deployable code; dev context stays separate | 2026-03-22 |
| Multi-agent orchestration by task type | Specialised agents > one-size-fits-all; dispatch chains prevent skipping quality steps | 2026-03-22 |
| Search-first: Adopt > Extend > Compose > Build | Never reinvent what exists; reduces code, bugs, maintenance | 2026-03-22 |
| 6-phase verification loop mandatory | Build → TypeCheck → Lint → Tests → Security → Diff — no "done" without all passing | 2026-03-22 |
| Auto-skill loading by task type | Eliminates "I forgot to load the skill" — matrix maps task → skills automatically | 2026-03-25 |
| Memory updates at session end + significant events | Context must survive sessions; gotchas prevent repeated mistakes | 2026-03-22 |

## Active Context

- **Branch**: `main`
- **Feature in progress**: none (scaffold only)
- **Known issues**: none
- **Appwrite collections**: none yet
- **Design system**: not yet initialized

## Session History

| Date | Summary |
|------|---------|
| 2026-03-25 | Added Persona Protocol, Auto-Skill Loading matrix, Memory System docs, updated plan/feature commands |
| 2026-03-22 | Initial repo, 8-role thinking, Design Philosophy, dual-repo publishing, multi-agent orchestration (from ECC), search-first, verification loop, immutability, hooks-over-prompts, AGENTS.md, README rewrite |
