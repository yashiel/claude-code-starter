# Project Memory

> Cross-session context. Read this FIRST at every session start. Updated at every session end.

## Latest Session

**Date**: 2026-07-06
**What was done**:
- Fixed stale shadcn/Appwrite references left from pre-MiHCM version (commands, skills, diagram templates, scripts/CLAUDE.md)
- Context optimization: five `@docs/` imports in CLAUDE.md converted to on-demand references with read-when triggers (only SECURITY.md stays always-loaded) — always-loaded instructions cut ~70% (9.1k → 2.7k words)
- Deduplicated CLAUDE.md: merged Enforced Libraries + Use This Not That + Banned into one USE/NEVER table; removed duplicate Principles section; moved Component Request template to `docs/component-requests/TEMPLATE.md`
- Compressed docs/AGENTS.md (−29%) and docs/ARCHITECTURE.md (−23%); both now point to CLAUDE.md as single source of truth for stack/structure
- Documented auth: IdentityServer4 (OIDC, Code + PKCE) + Microsoft Entra ID federated behind it; BFF holds tokens server-side, browser gets httpOnly session cookie only
- Fixed src/CLAUDE.md stale `apiFetch` description → `openapi-fetch` `createClient<paths>()`

**Decisions made**:
- MiHCM authorization depends on IdentityServer4 + Entra ID — all auth work targets this pair
- Detail docs load on demand; CLAUDE.md is the enforcement layer, docs/ the detail layer
- ⚠️ IdentityServer4 is EOL (Nov 2022, successor Duende) — flagged as upstream MiHCM platform concern, violates DEPS-02 spirit

**Project state**:
- Scaffold only — no application code yet
- Branch: `main`
- MiHCM MCP registered at project level (.mcp.json)

**What's next**:
- User to define PROJECT_NAME and one-line description
- Confirm auth topology assumption: IdentityServer4 as STS with Entra ID federated (vs direct Entra/MSAL)
- Set up npm registry access for `@yashiel` scope (GitHub PAT with `read:packages`)
- Install MiHCM packages: `pnpm add @yashiel/mihcm-ui @yashiel/mihcm-theme @yashiel/mihcm-tokens @yashiel/mihcm-icons`
- Initialize Next.js app with MiHCM theme integration

## Architecture Decisions

| Decision | Why | Date |
|----------|-----|------|
| Auth = IdentityServer4 (OIDC) + Microsoft Entra ID | MiHCM authorization depends on both. BFF pattern: Next.js confidential client, Code + PKCE, tokens server-side only | 2026-07-06 |
| Docs load on demand (only SECURITY.md stays @-imported) | Context optimization: always-loaded instructions cut ~70% (9.1k → 2.7k words); detail docs read when task triggers them | 2026-07-06 |
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
| 2026-07-06 | Context optimization (−70% always-loaded), stale shadcn/Appwrite cleanup, IdentityServer4 + Entra ID auth documented |
| 2026-05-18 | Major restructure: Karpathy principles, MiHCM design system, stack enforcement, CLAUDE.md rewrite |
