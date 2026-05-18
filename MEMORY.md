# Project Memory

> Cross-session context. Read this FIRST at every session start. Updated at every session end.

## Latest Session

**Date**: 2026-05-18
**What was done**:
- Rewrote CLAUDE.md: Karpathy Principles as #1 priority (Think, Simplify, Surgical, Verify)
- Enforced MiHCM Design System as sole UI library (banned shadcn/ui and all others)
- Enforced tech stack: Next.js 15+, Zod, Zustand, TanStack Query, Tailwind CSS 4, MiHCM
- Registered MiHCM MCP server (`https://designsystem.mihcm.com/mcp`)
- Moved detailed rules from CLAUDE.md to docs/CONVENTIONS.md, docs/ARCHITECTURE.md, docs/AGENTS.md
- Updated MCP setup runbook with MiHCM as primary
- Added `mcp__mihcm__*` and `pnpm` to allowed tools in settings.json
- Added MiHCM-first protocol to agent dispatch (always search MiHCM MCP before building UI)
- Added no-hardcoded-values enforcement (all values via MiHCM tokens or Tailwind semantic classes)

**Decisions made**:
- Karpathy Principles override all other rules
- MiHCM Design System is the only UI library allowed
- Zustand replaces ad-hoc client state management
- MiHCM MCP must auto-load for ANY UI/design work
- No hardcoded CSS/JS values — semantic tokens only

**Project state**:
- Scaffold only — no application code yet
- Branch: `main`
- MiHCM MCP registered at project level (.mcp.json)

**What's next**:
- User to define PROJECT_NAME and one-line description
- Set up npm registry access for `@yashiel` scope (GitHub PAT with `read:packages`)
- Install MiHCM packages: `pnpm add @yashiel/mihcm-ui @yashiel/mihcm-theme @yashiel/mihcm-tokens @yashiel/mihcm-icons`
- Initialize Next.js app with MiHCM theme integration
- Begin feature development

## Architecture Decisions

| Decision | Why | Date |
|----------|-----|------|
| Karpathy Principles = #1 priority | Think, simplify, surgical, verify — prevents bloat and AI slop | 2026-05-18 |
| MiHCM Design System = sole UI library | Company standard, consistency, 60+ components, MCP tooling | 2026-05-18 |
| Banned shadcn/ui and all other UI libs | Single design system, no fragmentation | 2026-05-18 |
| Zustand for client state | Lightweight, no boilerplate, replaces prop drilling | 2026-05-18 |
| No hardcoded values | All CSS/JS via MiHCM tokens or Tailwind semantic classes | 2026-05-18 |
| MiHCM MCP auto-loads for all UI work | Agent must search design system before building anything | 2026-05-18 |
| 8-role persona protocol | Prevents tunnel vision across system, code, DB, QA, design, UX | - |
| Dual-repo publishing | Cloud providers only need deployable code; dev context stays separate | - |
| Multi-agent orchestration | Specialised agents > one-size-fits-all | - |
| Search-first: MiHCM MCP > Adopt > Extend > Compose > Build | Never reinvent what exists, check design system first | 2026-05-18 |
| 6-phase verification loop | Build → TypeCheck → Lint → Tests → Security → Diff — all must pass | - |

## Session History

| Date | Summary |
|------|---------|
| 2026-05-18 | Major restructure: Karpathy principles, MiHCM design system, stack enforcement, CLAUDE.md rewrite |
