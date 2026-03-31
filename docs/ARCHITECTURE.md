# Architecture

## Overview
[PROJECT_NAME] — Next.js App Router + [YOUR_BACKEND] + shadcn/ui.

## Data Flow
```
Browser (RSC + Client + shadcn + TanStack Query)
  ↓
Next.js Server (Server Actions + Route Handlers + Middleware)
  ↓
[YOUR_BACKEND] (Auth/DB/Storage/APIs)
```

## MCP Integration Points
```
Developer (Claude Code)
  ├── Figma MCP ──→ design context, screenshots, FigJam diagrams
  ├── Vercel MCP ──→ deployments, preview URLs
  ├── Sentry MCP ──→ error tracking, debugging
  └── Context7 MCP ──→ latest docs for any library
```

**MCP = development workflow. SDK = application code.**

## Component Hierarchy
`components/ui/` — shadcn CLI-managed primitives · `components/features/` — domain composites from ui/

## Auth Flow
Form → Server Action → [Auth Provider] session → httpOnly cookie → middleware → authenticated Server Components

## Read/Write Flows
**Reads**: Server Component → authenticated client → query → render. Parallel: `Promise.all()` or `<Suspense>`.
**Writes**: Form → Server Action → zod validate → backend SDK → `revalidatePath()` → `redirect()` OUTSIDE try/catch.

## Deployment

### Dual-Repo Strategy
```
{project-name}          ← Full project (CLAUDE.md, docs/, tasks/, tools/, src/, everything)
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
shadcn > library (source ownership) · Server Components first (no waterfalls) · TanStack Query only when needed · Dual-repo (full context + clean deploy) · See `docs/decisions/`
