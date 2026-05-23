# [PROJECT_NAME]

> [One-line description]

## Karpathy Principles (HIGHEST PRIORITY)

These four principles override everything else. Every line of code, every decision, every response must pass through these gates first.

### 1. Think Before Coding
State assumptions explicitly. If uncertain, ask. If multiple interpretations exist, present them. If a simpler approach exists, say so. If something is unclear, stop and name what is confusing. Never hide confusion. Surface tradeoffs.

### 2. Simplicity First
Minimum code that solves the problem. Nothing speculative. No features beyond what was asked. No abstractions for single-use code. No "flexibility" or "configurability" that was not requested. No error handling for impossible scenarios. If 200 lines could be 50, rewrite it.

### 3. Surgical Changes
Touch only what you must. Clean up only your own mess. Do not "improve" adjacent code or formatting. Do not refactor things that are not broken. Match existing style, even if you would do it differently. If you notice dead code, mention it — do not delete it uninstructed.

### 4. Goal-Driven Execution
Transform every task into verifiable goals. "Add validation" becomes "write tests, then make them pass." "Fix the bug" becomes "reproduce it in a test, then fix." "Refactor X" becomes "ensure tests pass before and after." Define success criteria. Loop until verified.

### Supporting Karpathy Insights
- **Context Engineering > Prompt Engineering** — fill the context window with the right information for the next step
- **Verifiability** — automate what you can verify. If a task has tests, it is optimizable.
- **Build Simple to Complex** — add one verified component at a time. Never introduce unverified complexity all at once.
- **Don't Be a Hero** — use proven patterns and existing solutions before inventing. Adopt > Extend > Compose > Build.
- **Agents Are Fallible** — AI-generated code can be bloated, awkwardly abstracted, brittle. Always review.
- **Outsource Thinking, Not Understanding** — human judgment remains irreplaceable for taste and system comprehension.

## Stack (ENFORCED — no exceptions)

### Core
| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| Framework | Next.js (App Router) | 16.x | TypeScript strict mode |
| Runtime | React | 19.x | Server Components default |
| Language | TypeScript | 6.x | Strict mode, no `any` |
| Validation | Zod | 4.x | All input boundaries |
| State (client) | Zustand | 5.x | Client-side state only |
| Data fetching (client) | TanStack Query | 5.x | Client polling/optimistic only |
| Styling | Tailwind CSS | 4.x | Semantic tokens only, no hardcoded values |
| Design System | MiHCM Design System (`@yashiel/mihcm-ui`) | latest | ALL UI components from here |
| Icons | MiHCM Icons (`@yashiel/mihcm-icons`) | latest | 1,703 Lucide-based icons |
| Tokens | MiHCM Tokens (`@yashiel/mihcm-tokens`) | latest | CSS variables + TS constants |
| Theme | MiHCM Theme (`@yashiel/mihcm-theme`) | latest | Tailwind 4 preset + globals.css |
| AI UI | MiHCM AI UI (`@yashiel/mihcm-ai-ui`) | latest | Zod-validated generative UI |
| CLI | MiHCM CLI (`@yashiel/mihcm-cli`) | latest | `npx @yashiel/mihcm-cli add <Component>` |
| Deployment | Vercel | — | Git-push deploys |

### Enforced Libraries (use these — never build from scratch)
| Need | Library | Version | Notes |
|------|---------|---------|-------|
| Forms | `@tanstack/react-form` + `zod` | 1.x / 4.x | TanStack ecosystem. Built-in Zod validation. |
| Tables | `@tanstack/react-table` | 8.x | Headless — render with MiHCM `DataTable`/`Table` |
| Virtualization | `@tanstack/react-virtual` | 3.x | Large lists/grids — never render 100+ items without it |
| i18n | `next-intl` | 4.x | All user-facing strings. No hardcoded text. |
| Dates | `date-fns` + `date-fns-tz` | 4.x / 3.x | Date manipulation + timezone. Not `moment`. Not `dayjs`. |
| Data Visualization | `d3` | 7.x | Charts, graphs, custom viz. Bind MiHCM tokens for colors. |
| Drag & Drop | `@dnd-kit/core` | 6.x | Sortable lists, kanban boards, reorderable UI |
| Command Palette | `cmdk` | 1.x | Command menu / spotlight search. Pair with MiHCM `Command`. |
| Rich Text Editor | `lexical` + MiHCM `RichTextEditor` | 0.x | MiHCM wraps Lexical. Use MiHCM component first. |
| Animation | `motion` | 12.x | Transitions, layout animations. Use MiHCM motion tokens. |
| File Upload | `react-dropzone` | 15.x | Drag-and-drop file uploads |
| Error Monitoring | `@sentry/nextjs` | 10.x | Error tracking, performance monitoring |
| Analytics | `posthog-js` | 1.x | Product analytics, feature flags |
| Unit/Integration Tests | `vitest` + `@testing-library/react` | 4.x / 16.x | All component and unit tests |
| E2E Tests | `playwright` | 1.x | End-to-end browser tests |
| URL State | `nuqs` | 2.x | Type-safe search params state for Next.js. Replaces `useState` + manual URL sync. |
| API Client | `openapi-fetch` + `openapi-typescript` | 0.x / 7.x | Type-safe fetch from OpenAPI schemas. `openapi-typescript` generates types (dev). |
| Emails | `@react-email` | 4.x | Transactional email templates |

### Use This, Not That
| When you need... | USE | NEVER build/use |
|-----------------|-----|-----------------|
| A form | `@tanstack/react-form` + `zod` + MiHCM `Form`/`Input` | Custom form state, react-hook-form, uncontrolled inputs |
| A data table | `@tanstack/react-table` + MiHCM `DataTable` | Custom table sorting/filtering/pagination logic |
| A long list | `@tanstack/react-virtual` | Rendering all items, custom virtual scroll |
| Translated text | `next-intl` `useTranslations()` | Hardcoded strings, custom i18n |
| Date formatting | `date-fns` `format()` / `formatDistance()` | `new Date().toLocaleDateString()`, moment, dayjs |
| A chart | `d3` + MiHCM color tokens | Chart.js, Recharts, custom SVG |
| Drag reordering | `@dnd-kit/core` | `react-beautiful-dnd`, custom drag handlers |
| Command palette | `cmdk` + MiHCM `Command` | Custom search dialogs |
| Rich text | MiHCM `RichTextEditor` (Lexical) | Textarea for rich content, Quill, Slate, tiptap |
| Animations | `motion` + MiHCM motion tokens | Raw CSS transitions, react-spring, GSAP |
| File drop zone | `react-dropzone` | Custom drag event handlers |
| Error tracking | `@sentry/nextjs` | `console.error` in production, custom error services |
| Analytics | `posthog-js` | Custom event tracking, Google Analytics |
| Tests | `vitest` + `@testing-library/react` | Jest (unless already configured), Enzyme |
| E2E tests | `playwright` | Cypress, Puppeteer |
| URL search params state | `nuqs` `useQueryState()` / `useQueryStates()` | `useState` + `useSearchParams` + manual URL sync |
| API calls to OpenAPI backends | `openapi-fetch` `createClient<paths>()` | Manual `fetch` wrappers, hand-typed request/response types |
| Email templates | `@react-email` | Raw HTML email strings |
| UI components | `@yashiel/mihcm-ui` | shadcn, Radix direct, MUI, Chakra, Ant, Mantine |

### MiHCM-First Rule (EVERY UI element)
Every button, input, dialog, table, card, sidebar, form — everything visible to the user — MUST be a MiHCM component or a composition of MiHCM components. Never build a UI primitive from scratch. Never use raw HTML elements when a MiHCM component exists. Check MiHCM MCP (`mihcm_search_components`) before writing any JSX.

**Decision order**: MiHCM component directly → compose MiHCM components → extend MiHCM component with wrapper → **request new component** (never build from scratch).

### Component Request Protocol (when MiHCM doesn't have what you need)
If no MiHCM component exists and composing/extending won't work, **do NOT build a custom component**. Instead, create a handoff document at `docs/component-requests/[ComponentName].md` for the design team:

```markdown
# Component Request: [ComponentName]

## Problem
What UI need this component solves. What the user is trying to do.

## Proposed Behavior
- Functional requirements (what it does)
- Interaction states (hover, focus, disabled, loading, error, empty)
- Responsive behavior (mobile → desktop)

## Props API (suggested)
| Prop | Type | Default | Description |
|------|------|---------|-------------|
| variant | 'default' \| 'outline' | 'default' | Visual style |

## Composition
Which existing MiHCM components it should build on internally.

## Accessibility
Keyboard nav, ARIA roles, focus management, screen reader behavior.

## Design References
Links to 3-5 real production examples (Linear, Stripe, Vercel, Notion, etc).

## Priority
Critical / High / Medium / Low — and why.

## Requested By
Date, project, and context for why this is needed.
```

**Rules**:
- Always search MiHCM MCP first (`mihcm_search_components`) — the component may already exist
- Always try composing existing components before requesting a new one
- Use a temporary placeholder (MiHCM `Card` or `Alert` with a "Component pending" message) in the UI while waiting
- Never build a production-quality custom primitive — that's the design system team's job
- Track all requests in `tasks/todo.md` under a "Component Requests" section

### Banned
- shadcn/ui — DO NOT use. Not installed. Not allowed.
- Radix UI (direct) — use MiHCM wrappers instead (they wrap Radix internally)
- Material UI, Chakra UI, Ant Design, Mantine, or any other component library
- Hardcoded CSS values (hex colors, px values, raw spacing) — use MiHCM tokens and Tailwind semantic classes
- Any UI primitive not from `@yashiel/mihcm-ui`
- Building raw `<button>`, `<input>`, `<table>`, `<dialog>` — use MiHCM components
- moment.js, dayjs — use `date-fns`
- Jest — use `vitest` (unless project already uses Jest)
- Cypress — use `playwright`
- Chart.js, Recharts — use `d3`
- Quill, Slate, tiptap — use MiHCM `RichTextEditor` (Lexical)
- react-spring, GSAP — use `motion`
- react-beautiful-dnd — use `@dnd-kit/core`
- react-hook-form, @hookform/resolvers — use `@tanstack/react-form`

### MiHCM Import Rules
```tsx
// CORRECT — subpath import (tree-shaking)
import { Button } from '@yashiel/mihcm-ui/Button';
import { DataTable } from '@yashiel/mihcm-ui/DataTable';

// WRONG — barrel import pulls everything
import { Button } from '@yashiel/mihcm-ui';
```

## MCP Servers (connected tools)

| Server | Purpose | Auto-load When |
|--------|---------|---------------|
| **MiHCM** | Component search, token lookup, snippet review, contract validation, RSC boundary check | ANY UI/design work (ALWAYS) |
| **Figma** | Design-to-code context, screenshots, diagrams | Implementing designs, design references |
| **Vercel** | Deployments, toolbar threads | Deploy, preview, production issues |
| **Context7** | Up-to-date docs for any library | ANY library/framework question |

Setup guide: `docs/runbooks/mcp-setup.md`

**MCP-First Rule**: When a connected MCP tool can handle the task, use it. MiHCM MCP for component details > guessing. Context7 for latest docs > training data. Figma MCP for design context > imagination.

**MiHCM MCP is MANDATORY for all design/UI work.** Before building any component, search the MiHCM design system first.

## Auto-Loading Protocol (MANDATORY)

### MCP Auto-Load
| Trigger | MCP to Load |
|---------|------------|
| ANY UI/component/design work | MiHCM (always), Figma (if design ref exists) |
| Library/framework questions | Context7 |
| Deployment/preview | Vercel |
| Design-to-code | MiHCM + Figma |

### Skill Auto-Load
| Task | Skills |
|------|--------|
| ANY UI | `frontend-design`, `refactoring-ui`, `ux-heuristics`, `microinteractions`, `web-design-guidelines`, `design-auditor`, `top-design` + MiHCM MCP |
| Page/layout | Above + `web-typography`, `design-everyday-things` |
| Planning | `plan`, `brainstorm` |
| Bug fix | `systematic-debugging`, `fix` |
| Code review | `review`, `clean-code` |
| Auth/security | `owasp-security`, `vibesec-skill` |
| Database | `database-schema-designer` + `docs/security-playbook.md` §10 |
| React | `vercel-react-best-practices`, `vercel-composition-patterns` |
| Next.js | `next-best-practices`, `next-cache-components` |
| MiHCM components | MiHCM MCP tools: `mihcm_search_components`, `mihcm_get_component` |
| Deploy | `deploy-to-vercel` |
| Tests | `webapp-testing`, `agent-browser` |
| Research | `firecrawl-search`, `firecrawl-scrape`, `firecrawl-crawl` |

**Rules**: 1% chance a skill applies = load it. Multiple apply = load ALL. MiHCM MCP ALWAYS for UI.

## Thinking Mindset (8 Lenses)

Run all 8 before writing code. Details in `docs/CONVENTIONS.md`.

| Role | Core Question |
|------|--------------|
| System Architect | Fits system? Data flow? Scale 10x/100x? |
| Software Engineer | Clean? SOLID? Types? Edge cases? |
| Database Engineer | Schema? Indexes? Migrations reversible? |
| QA Engineer | What breaks? Null? 10k? Unicode? Auth bypass? |
| Designer | MiHCM components used? No hardcoded values? Production-grade? |
| Creative Director | Typography hierarchy? Color story via MiHCM tokens? Spacing rhythm? |
| UX Designer | Frictionless? Cognitive load? Feedback? Progressive disclosure? |
| Frontend Developer | RSC boundaries? Accessible? Mobile-first? All states? |

## No Hardcoded Values (ENFORCED)

| Wrong | Right |
|-------|-------|
| `color: #003385` | `text-primary` / `var(--color-primary)` |
| `padding: 16px` | `p-4` |
| `font-size: 14px` | `text-body-sm` (MiHCM token) |
| `gap: 8px` | `gap-2` |
| `max-width: 1280px` | `max-w-screen-xl` |
| `transition: 0.3s` | `duration-medium` (MiHCM motion token) |

## Workflow
1. **Think First** — Karpathy #1. State assumptions, surface tradeoffs.
2. **Search First** — MiHCM MCP for components/tokens → packages → skills. Adopt > Extend > Compose > Build.
3. **Research First** — Context7/WebSearch for latest docs. MiHCM docs for components.
4. **Skills First** — 1% chance = load it. MiHCM MCP always for UI.
5. **Plan** — any 2+ step task. All 8 lenses.
6. **Build Simply** — Karpathy #2. Minimum code. One verified piece at a time.
7. **Surgical Execution** — Karpathy #3. Touch only what's needed.
8. **Verify** — Karpathy #4. 6-phase verification loop.
9. **Learn** — gotcha → pattern → rule. Update `tasks/gotchas.md`.

## Verification Loop

| Phase | Command | Fail Action |
|-------|---------|-------------|
| 1. Build | `npm run build` | Fix, re-run |
| 2. TypeCheck | `npx tsc --noEmit` | Fix, re-run |
| 3. Lint | `npm run lint` | Fix, re-run |
| 4. Tests | `npm test` | Fix, re-run |
| 5. Security | `owasp-security` / `vibesec-skill` | Fix CRITICAL |
| 6. Diff Review | `git diff` | Remove dead code, check secrets |

If ANY phase fails, task is NOT done.

## Principles
- **Karpathy First** — think, simplify, be surgical, verify. Always.
- **MiHCM Design System** — every UI component, token, icon from here. No exceptions.
- **No Hardcoded Values** — semantic tokens for everything.
- **Simplicity** — if 200 lines could be 50, rewrite.
- **Immutability** — new objects, never mutate. Pure functions over side effects.
- **Search First** — MiHCM MCP → packages → skills. Never reinvent.
- **Verification > Generation** — proving > writing.
- **Memory Is Continuous** — update `MEMORY.md` at session end.

## Structure
```
src/
  web/                     → MiHCM Next.js project
    app/                   → App Router (layout, page, loading, error, not-found, global-error)
      (auth)/              → login, register
      (dashboard)/         → protected routes
      api/                 → route handlers
    components/ds/         → MiHCM CLI-copied components (copy-paste mode)
    components/features/   → domain composites from MiHCM components
    lib/                   → api/ (openapi-fetch client + domain modules, all server-only), auth/ (session, middleware), schemas/ (zod, shared client+server), env.ts, errors.ts, utils.ts
    actions/               → Server Actions by domain
    hooks/                 → use-[name].ts
    stores/                → Zustand stores by domain
    providers/             → QueryClientProvider, ThemeProvider, NuqsAdapter
    types/                 → shared types
  mobile/                  → Expo (React Native) project
    app/                   → Expo Router screens
    components/            → MiHCM native components
    hooks/                 → mobile-specific hooks
    stores/                → Zustand stores (shared patterns with web)
    types/                 → shared types
  static/                  → Plain HTML/CSS/JS project
    pages/                 → HTML pages
    css/                   → stylesheets (MiHCM tokens via CSS variables)
    js/                    → vanilla JavaScript
    assets/                → images, fonts, icons
tasks/                     → todo.md, gotchas.md
docs/                      → ARCHITECTURE, CONVENTIONS, SECURITY, security-playbook, decisions/, runbooks/, diagrams/, component-requests/
```

## BFF Architecture (ENFORCED)
Browser NEVER calls backend APIs directly. All upstream calls go through Next.js (RSC / Server Action / Route Handler). Next.js holds tokens, aggregates, and returns page-shaped data.
`Browser (cookie) → Next.js → MIHCM APIs`
- Every `lib/api/*` file MUST start with `import 'server-only'`
- All upstream fetches use `openapi-fetch` client (type-safe, generated from OpenAPI spec) → domain modules → pages. Never call the API client from a page directly.
- Generate types: `npx openapi-typescript ./specs/api.yaml -o ./src/web/lib/api/v1.d.ts`
- Create client in `lib/api/client.ts` with `createClient<paths>()` from `openapi-fetch`
- Upstream tokens live in server-only env vars — NEVER `NEXT_PUBLIC_` for secrets

## Data Fetching Rules
**Rule 1 — Parallelize** (sequential awaits = bug): `const [a, b] = await Promise.all([getFoo(), getBar()]);`
**Rule 2 — Stream** — wrap slow components in `<Suspense fallback={<Skeleton />}>`. Skeleton MUST match component dimensions (prevents layout shift).
**Rule 3 — Deduplicate** — wrap fetch functions in `cache()` so multiple components in the same render share one upstream call.

**Caching** (always set `next: { revalidate, tags }`):
| Data | revalidate |
|------|-----------|
| Profile, org chart | 300s |
| Leave balance | 60s |
| Attendance today | 30s |
| Notifications / real-time | 0 — use TanStack Query |
| Reference data (depts, roles) | 3600s |

Invalidate precisely: `revalidateTag('employees:list')` — not `revalidatePath('/')`. Never over-invalidate.

**TanStack Query staleTimes**: profile `5min` · leave `60s` · attendance/notifications `0`

**RSC → Client hydration** (interactive pages — avoids double-fetch): `prefetchQuery` on server → `HydrationBoundary state={dehydrate(qc)}` wraps the client component.

**Aggregation** (dashboard / multi-source pages): `Promise.allSettled` so one failed call doesn't kill the whole page. Return `null` for failed segments.

**Trim payloads**: return only fields the component needs. Never pass full upstream objects to the client.

## Mutations (Server Actions First)
Prefer Server Actions over Route Handlers for forms. Sequence: `requireSession()` → `Schema.safeParse()` → `apiFetch()` → `revalidateTag()` → `redirect()` (OUTSIDE try/catch).
Route Handlers only for: external webhooks, mobile apps, third-party consumers.
Errors: log full detail server-side, return generic message to client. Never expose stack traces or upstream error messages.

## Decision Trees
**Where to fetch?** On page load → RSC + `revalidate` + tags. Interactive/paginated/polled → TanStack Query (optionally prefetch with `HydrationBoundary`).
**Server Action vs Route Handler?** Form in this app → Server Action. External / mobile / webhook → Route Handler.
**`'use client'`?** Needs state, effects, event handlers, or browser API → `'use client'` (push directive as deep as possible). Otherwise → RSC.
**URL state or React state?** Filterable/sortable/paginated/shareable → `nuqs` `useQueryState()`. Ephemeral UI-only (modals, tooltips) → Zustand or `useState`.

## Commands
```bash
npm run dev · npm run build · npm run lint · npm test · npx tsc --noEmit · npx @yashiel/mihcm-cli add <Component> · npx openapi-typescript ./specs/api.yaml -o ./src/web/lib/api/v1.d.ts
```

## Quality Gate (every change)
- **Karpathy**: think first? simple? surgical? verified?
- **MiHCM**: ALL components from `@yashiel/mihcm-ui`? No hardcoded values?
- **Verification**: 6-phase loop passed?
- **Code**: no dead code. Functions <50 lines. Immutable. See `docs/CONVENTIONS.md`.
- **Security**: `docs/security-playbook.md` rules. See `docs/SECURITY.md`.
- **Accessibility**: WCAG 2.1 AA. See `docs/CONVENTIONS.md`.
- **Search Audit**: checked MiHCM MCP and packages before custom code?
- **BFF**: all `lib/api/*` have `import 'server-only'`? Using `openapi-fetch` client (not raw fetch)? No upstream tokens in client bundle? No `NEXT_PUBLIC_` secrets?
- **URL State**: filters/search/pagination use `nuqs` (not `useState` + manual URL sync)?
- **Fetching**: independent fetches parallelized? Slow sections in `<Suspense>`? Cache tags set and invalidated precisely?
- **TypeScript**: strict mode, no `any`, no `@ts-ignore` without explanation?
- **Zod**: every input boundary validated (client→server, upstream→server, webhooks)?

## Memory System
**Session start**: read `MEMORY.md` → `tasks/todo.md` → `tasks/gotchas.md` → announce context.
**Session end**: update all three.

## Diagrams (`docs/diagrams/`, Mermaid)
Update when architecture changes. ERD + Sequence = highest priority. See `docs/ARCHITECTURE.md`.

## Docs
- **@docs/AGENTS.md** — multi-agent orchestration, dispatch rules
- **@docs/ARCHITECTURE.md** — system design, data flow, MiHCM integration, diagrams
- **@docs/CONVENTIONS.md** — naming, RSC, MiHCM usage, code quality, design standards, accessibility, 8-lens details
- **@docs/security-playbook.md** — 100+ rules (AUTH/INJ/AUTHZ)
- **@docs/SECURITY.md** — 24-rule quick-ref
- **@docs/runbooks/mcp-setup.md** — MCP server setup including MiHCM

## Publishing (Dual-Repo)
1. **`{project-name}`** — everything (CLAUDE.md, docs/, tasks/, src/, configs)
2. **`{project-name}-app`** — deployable only (src/, configs, package.json)

## Compaction
Preserve: modified files, failing tests, branch + task context, todo/gotchas contents, agent chain state, MiHCM component context
