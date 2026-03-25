# Architecture

## Overview
[PROJECT_NAME] — Next.js App Router + Appwrite BaaS + shadcn/ui + Stripe/PayPal/TNG payments.

## Data Flow
```
Browser (RSC + Client + shadcn + TanStack Query)
  ↓
Next.js Server (Server Actions + Route Handlers + Middleware)
  ↓                    ↓
node-appwrite SDK    Payment SDKs (Stripe/PayPal/TNG RSA256)
  ↓                    ↓
Appwrite Cloud       Stripe · PayPal · TNG eWallet
(Auth/DB/Storage)
```

## MCP Integration Points
```
Developer (Claude Code)
  ├── Appwrite MCP ──→ DB schema, users, storage, functions (direct management)
  ├── Appwrite Docs MCP ──→ latest SDK/API documentation
  ├── Figma MCP ──→ design context, screenshots, Code Connect, FigJam diagrams
  ├── Vercel MCP ──→ deployments, preview URLs, toolbar threads
  ├── Sentry MCP ──→ error tracking, debugging production issues
  ├── Context7 MCP ──→ latest docs for any library (Next.js, React, etc.)
  ├── Gmail MCP ──→ email drafts, labels
  └── Canva MCP ──→ graphics, marketing visuals
```

**When to use MCP vs SDK**:
- **Appwrite MCP** → exploring schemas, creating collections, managing users during development
- **Appwrite SDK (`node-appwrite`)** → application code (Server Components, Server Actions)
- **Figma MCP** → reading designs, extracting design tokens, generating code from Figma
- **Figma `generate_diagram`** → creating flowcharts, sequence diagrams, state diagrams in FigJam
- **Context7 MCP** → looking up current API docs instead of relying on training data

## Component Hierarchy
`components/ui/` — shadcn CLI-managed primitives · `components/features/` — domain composites from ui/

## Auth Flow
Form → Server Action → Appwrite session → httpOnly cookie → middleware checks → `createSessionClient()` in Server Components

## Read/Write/Payment Flows
**Reads**: Server Component → `createSessionClient()` → `databases.listDocuments()` → render. Parallel: `Promise.all()` or `<Suspense>`.
**Writes**: Form → Server Action → zod → Appwrite SDK → `revalidatePath()` → `redirect()` OUTSIDE try/catch.
**Stripe**: Server Action → Checkout Session → redirect → webhook → verify sig → update Appwrite.
**PayPal**: Client `<PayPalButtons>` → `/api/paypal/create-order` → approve → `/api/paypal/capture-order` → update Appwrite.
**TNG**: Server Action → RSA256 `/v1/payments/pay` → `A` → redirect cashier → async notification → verify sig → update Appwrite. `U`: poll, NEVER mark failed.

## Env Vars
Public: `NEXT_PUBLIC_APPWRITE_ENDPOINT`, `NEXT_PUBLIC_APPWRITE_PROJECT`, `NEXT_PUBLIC_STRIPE_PUBLISHABLE_KEY`, `NEXT_PUBLIC_PAYPAL_CLIENT_ID`, `NEXT_PUBLIC_APP_URL`
Server-only: `NEXT_APPWRITE_KEY`, `STRIPE_SECRET_KEY`, `STRIPE_WEBHOOK_SECRET`, `PAYPAL_CLIENT_ID`, `PAYPAL_CLIENT_SECRET`, `PAYPAL_API_URL`, `TNG_CLIENT_ID`, `TNG_PARTNER_ID`, `TNG_PRIVATE_KEY`, `TNG_PUBLIC_KEY`, `TNG_API_URL`, `TNG_APP_ID`

## Deployment

### Dual-Repo Strategy
```
{project-name}          ← Full project (CLAUDE.md, docs/, tasks/, tools/, src/, everything)
{project-name}-app      ← Deployable app only (src/, configs, package.json — what cloud providers need)
```
- **Full repo** = development workspace, Claude Code context, planning, security docs
- **App repo** = production artifact, connected to cloud provider for auto-deploy
- Cloud providers (Vercel, Appwrite Sites, Heroku, Railway) deploy from the `-app` repo
- See `CLAUDE.md > Publishing Protocol` for exact steps

### Cloud Providers
Vercel: `deploy-to-vercel` skill or Vercel MCP, git-push deploys from `-app` repo, Edge middleware.
Appwrite Sites: CLI deploy, SSR adapter, console env vars.
Heroku/Railway: git-push deploys from `-app` repo, Procfile if needed.
CI: Push → Lint → TSC → Test → Build → Deploy (preview) → Merge → Deploy (prod)

## Diagrams (in `docs/diagrams/`, Mermaid format)

All diagrams MUST be kept current. When you change architecture, update affected diagrams.

| Diagram | File | Purpose | Update When |
|---------|------|---------|-------------|
| **ERD** | `diagrams/erd.md` | Entity relationships, Appwrite collections, attributes, cardinality | Any collection/attribute/relationship change |
| **Class** | `diagrams/class.md` | Services, models, interfaces, inheritance, dependencies | New service/model, major refactor |
| **Deployment** | `diagrams/deployment.md` | Infrastructure: Vercel, Appwrite Cloud, CDN, env separation | Provider, env, or infra change |
| **Use Case** | `diagrams/use-cases.md` | Actors (user, admin, system) and their interactions | New feature or user-facing flow |
| **Sequence** | `diagrams/sequences.md` | Auth flow, payment flows, API request lifecycle | New API/auth/payment flow |
| **Activity** | `diagrams/activities.md` | Business processes: checkout, onboarding, order lifecycle | New workflow or process change |
| **State Machine** | `diagrams/state-machines.md` | Stateful entities: order status, payment status, subscription | New stateful entity or status change |

### Diagram Format
Each file contains one or more Mermaid diagrams with a title and description:
```markdown
## [Diagram Name]
> [One-line description of what this shows]

```mermaid
[diagram code]
```

### Maintenance Rules
1. Create diagrams when the feature is FIRST built — not retroactively
2. Update ALL affected diagrams in the SAME commit as the code change
3. ERD and Sequence diagrams are highest priority — always current
4. Use Figma MCP `generate_diagram` for visual FigJam versions when needed
5. Reference diagram changes in `MEMORY.md` when architecture shifts

## Key Decisions
Appwrite > custom backend (BaaS) · shadcn > library (source ownership) · Server Components first (no waterfalls) · TanStack Query only when needed · Stripe Checkout (simplest PCI) · MCP for development workflow, SDK for application code · Dual-repo (full context + clean deploy) · See `docs/decisions/`
