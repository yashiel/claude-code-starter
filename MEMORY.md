# Project Memory

> Cross-session context log. Updated at the end of every session.
> Read this FIRST when starting a new session.

## Latest Session

**Date**: 2026-03-22
**What was done**:
- Created the project repository and published to GitHub as public repo
- URL: https://github.com/yashiel/claude-code-starter
- Updated CLAUDE.md with 8-role thinking mindset (System Architect, Software Engineer, DB Engineer, QA Engineer + Designer, Creative Director, UX Designer, Frontend Developer)
- Added mandatory Session Protocol (start/end procedures)
- Added Error Handling Protocol with architectural fix requirements
- Enforced planning-first workflow, skills-first approach, research-before-building
- Added Design Philosophy section — ZERO AI SLOP policy, accessibility-first, aesthetics-first
- Added AI Slop Detection Checklist to Quality Gate
- Added comprehensive Design Standards to CONVENTIONS.md (accessibility, typography, spacing, color, states, motion, design principles)
- Added memory discipline to principles and workflow
- Created this MEMORY.md for cross-session continuity

**Decisions made**:
- All 8 lenses (4 engineering + 4 design) must be applied before any code change
- Planning mode is mandatory for 2+ step tasks (lowered from 3)
- Skills must be checked and invoked before any action — design skills MANDATORY for all UI work
- Research real products (3-5 examples) before designing any interface
- Accessibility is non-negotiable: WCAG 2.1 AA, keyboard nav, screen reader, 44px touch targets
- No AI slop — every pixel intentional, production-grade, would-a-Creative-Director-approve standard
- Dual-repo publishing: full project repo + deployable app-only repo when publishing to GitHub
- Multi-agent orchestration: specialised agents dispatched by task type (feature/bugfix/refactor/UI chains)
- Search-first protocol: Adopt > Extend > Compose > Build — always check existing before writing custom
- 6-phase Verification Loop: Build → TypeCheck → Lint → Tests → Security → Diff — ALL must pass before done
- Immutability principle: new objects, never mutate; pure functions over side effects
- Hooks over prompts: deterministic enforcement via hooks > probabilistic LLM instructions
- Observations evolve: gotcha (0.3) → pattern (0.6) → rule (0.9) — confidence-weighted learning
- Memory updates are mandatory at session end

**Project state**:
- Scaffold only — no application code yet
- All docs in place: ARCHITECTURE, CONVENTIONS, SECURITY, PAYMENTS, security-playbook
- Custom skills: catchup, plan, review
- Custom commands: catchup, commit, feature, fix, pr
- Branch: `main`

**What's next**:
- User to define PROJECT_NAME and one-line description
- Initialize Next.js app (`npx create-next-app@latest`)
- Set up Appwrite project and configure env vars
- Install and configure shadcn/ui
- Begin feature development

## Session History

| Date | Summary |
|------|---------|
| 2026-03-22 | Initial repo, 8-role thinking, Design Philosophy, dual-repo publishing, multi-agent orchestration (from ECC), search-first, verification loop, immutability, hooks-over-prompts, AGENTS.md |
