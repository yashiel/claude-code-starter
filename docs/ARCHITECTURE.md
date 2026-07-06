# Architecture

## Overview
[PROJECT_NAME] — Next.js App Router + [YOUR_BACKEND] + MiHCM Design System. Full stack + enforced libraries: `CLAUDE.md > Stack` (single source of truth).

## Data Flow
```
Browser (RSC + Client + MiHCM UI + TanStack Query + Zustand + nuqs)
  ↓
Next.js Server (Server Actions + Route Handlers + Middleware)
  ↓  openapi-fetch (type-safe, generated from OpenAPI spec)
[YOUR_BACKEND] (Auth/DB/Storage/APIs)
```

## MCP Integration Points
```
Developer (Claude Code)
  ├── MiHCM MCP ──→ component search, token lookup, snippet review, RSC checks
  ├── Figma MCP ──→ design context, screenshots, FigJam diagrams
  ├── Vercel MCP ──→ deployments, preview URLs
  └── Context7 MCP ──→ latest docs for any library
```
**MiHCM MCP = mandatory for all UI work. Context7 = mandatory for library questions.**

## Monorepo Structure
`src/web` — MiHCM Next.js (App Router, RSC, Server Actions) · `src/mobile` — Expo (React Native, NativeWind, Expo Router) · `src/static` — plain HTML/CSS/JS with MiHCM tokens via CSS variables.
All three share MiHCM design tokens. Zustand stores and Zod schemas shareable between `web/` and `mobile/` via a shared types/schemas layer. Full tree: `CLAUDE.md > Structure`.

## Component Hierarchy (web/)
`@yashiel/mihcm-ui` — design system components (subpath imports) · `web/components/ds/` — CLI-copied MiHCM components · `web/components/features/` — domain composites.

## Auth Flow (IdentityServer4 + Microsoft Entra ID)
MiHCM authorization depends on IdentityServer4 (OIDC/OAuth2 token service) with Microsoft Entra ID as the corporate identity provider federated behind it.

```
Browser → Next.js /login → redirect to IdentityServer4 (authorize, Code + PKCE)
  → IdentityServer4 federates to Entra ID (corporate SSO, MFA policy)
  → callback with auth code → Next.js (confidential client) exchanges code for tokens
  → access/refresh tokens stored SERVER-SIDE only → httpOnly session cookie to browser
  → middleware validates session on every request
  → Server Components / Server Actions call MIHCM APIs with bearer access token
  → refresh token rotation handled server-side (AUTH-01)
```
Rules: tokens never in the client bundle, localStorage, or `NEXT_PUBLIC_` vars (CRYPTO-03) · cookie = HttpOnly + Secure + SameSite (AUTH cookie rules, playbook §3) · session regenerated after login (AUTH-05) · role/permission claims enforced server-side (AUTHZ-01).

## Read/Write Flows
**Reads**: Server Component → authenticated client → query → render. Parallel: `Promise.all()` or `<Suspense>`.
**Writes**: Form → Server Action → Zod validate → backend SDK → `revalidatePath()` → `redirect()` OUTSIDE try/catch.

## State Management
Server state: Server Components (primary), TanStack Query (client polling/optimistic) · Client: Zustand `src/stores/[domain].ts` · URL: `nuqs` (filters, search, pagination, sort, tabs) · Forms: TanStack React Form via MiHCM `Form`. Decision trees: `CLAUDE.md > Decision Trees`.

## Deployment
**Dual-repo**: `{project-name}` (full context) + `{project-name}-app` (deployable only) — see `CLAUDE.md > Publishing`.
Vercel git-push deploys (`deploy-to-vercel` skill or Vercel MCP). CI: Push → Lint → TSC → Test → Build → Deploy (preview) → Merge → Deploy (prod).

## Diagrams (in `docs/diagrams/`, Mermaid format)

Keep current. Update affected diagrams in the SAME commit as the change. Create when first built.

| Diagram | File | Update When |
|---------|------|-------------|
| **ERD** (highest priority) | `diagrams/erd.md` | Any table/relationship change |
| **Sequence** (highest priority) | `diagrams/sequences.md` | New API/auth flow |
| **Class** | `diagrams/class.md` | New service/model, major refactor |
| **Deployment** | `diagrams/deployment.md` | Infrastructure/provider change |
| **Use Case** | `diagrams/use-cases.md` | New feature or user-facing flow |
| **Activity** | `diagrams/activities.md` | New workflow or process |
| **State Machine** | `diagrams/state-machines.md` | New stateful entity |

## Key Decisions
MiHCM Design System > any other UI library (company standard, consistency) · Server Components first (no waterfalls) · Zustand for client state (lightweight, no boilerplate) · TanStack Query for server-state sync · Dual-repo (full context + clean deploy) · ADRs in `docs/decisions/`
