# Architecture

## Overview
[PROJECT_NAME] — Next.js App Router + [YOUR_BACKEND] + MiHCM Design System.

## Stack
Next.js 15+ (App Router, React 19) · TypeScript strict · Zod · Zustand · TanStack Query · Tailwind CSS 4 · MiHCM Design System (`@yashiel/mihcm-ui`, `@yashiel/mihcm-theme`, `@yashiel/mihcm-tokens`, `@yashiel/mihcm-icons`) · Vercel

## Data Flow
```
Browser (RSC + Client + MiHCM UI + TanStack Query + Zustand)
  ↓
Next.js Server (Server Actions + Route Handlers + Middleware)
  ↓
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
```
src/
  web/       → MiHCM Next.js (App Router, RSC, Server Actions)
  mobile/    → Expo (React Native, NativeWind, Expo Router)
  static/    → Plain HTML/CSS/JS (MiHCM tokens via CSS variables)
```

All three targets share MiHCM design tokens and follow MiHCM design system conventions. Zustand stores and Zod schemas can be shared between `web/` and `mobile/` via a shared types/schemas layer.

## Component Hierarchy (web/)
`@yashiel/mihcm-ui` — design system components (imported via subpath) · `web/components/ds/` — CLI-copied MiHCM components (copy-paste mode) · `web/components/features/` — domain composites from MiHCM components

## Auth Flow
Form → Server Action → [Auth Provider] session → httpOnly cookie → middleware → authenticated Server Components

## Read/Write Flows
**Reads**: Server Component → authenticated client → query → render. Parallel: `Promise.all()` or `<Suspense>`.
**Writes**: Form → Server Action → Zod validate → backend SDK → `revalidatePath()` → `redirect()` OUTSIDE try/catch.

## State Management
- **Server state**: Server Components (primary), TanStack Query (client polling/optimistic)
- **Client state**: Zustand stores in `src/stores/[domain].ts`
- **Form state**: TanStack React Form v1 (via MiHCM Form component) or controlled components

## Deployment

### Dual-Repo Strategy
```
{project-name}          ← Full project (CLAUDE.md, docs/, tasks/, src/, everything)
{project-name}-app      ← Deployable app only (src/, configs, package.json)
```
See `CLAUDE.md > Publishing Protocol` for exact steps.

### Cloud Providers
Vercel: `deploy-to-vercel` skill or Vercel MCP, git-push deploys.
CI: Push → Lint → TSC → Test → Build → Deploy (preview) → Merge → Deploy (prod)

## Diagrams (in `docs/diagrams/`, Mermaid format)

All diagrams MUST be kept current. Update affected diagrams when architecture changes.

| Diagram | File | Update When |
|---------|------|-------------|
| **ERD** | `diagrams/erd.md` | Any table/relationship change |
| **Class** | `diagrams/class.md` | New service/model, major refactor |
| **Deployment** | `diagrams/deployment.md` | Infrastructure/provider change |
| **Use Case** | `diagrams/use-cases.md` | New feature or user-facing flow |
| **Sequence** | `diagrams/sequences.md` | New API/auth flow |
| **Activity** | `diagrams/activities.md` | New workflow or process |
| **State Machine** | `diagrams/state-machines.md` | New stateful entity |

**Rules**: Create when first built. Update in SAME commit. ERD + Sequence = highest priority.

## Key Decisions
MiHCM Design System > any other UI library (company standard, consistency) · Server Components first (no waterfalls) · Zustand for client state (lightweight, no boilerplate) · TanStack Query for server-state sync · Dual-repo (full context + clean deploy) · See `docs/decisions/`
