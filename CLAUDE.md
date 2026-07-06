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
| Auth | IdentityServer4 (OIDC/OAuth2) + Microsoft Entra ID | — | MiHCM authorization depends on both. Auth Code + PKCE via BFF; tokens never reach browser |
| State (client) | Zustand | 5.x | Client-side state only |
| Data fetching (client) | TanStack Query | 5.x | Client polling/optimistic only |
| Styling | Tailwind CSS | 4.x | Semantic tokens only, no hardcoded values |
| Design System | MiHCM suite (`@yashiel/mihcm-ui`, `-icons` 1,703 Lucide-based, `-tokens`, `-theme`, `-ai-ui`, `-cli`) | latest | ALL UI from here. `npx @yashiel/mihcm-cli add <Component>` |
| Deployment | Vercel | — | Git-push deploys |

### Libraries (USE these — NEVER the alternatives, never build from scratch)
| Need | USE | Version | NEVER |
|------|-----|---------|-------|
| Forms | `@tanstack/react-form` + `zod` + MiHCM `Form`/`Input` | 1.x / 4.x | react-hook-form, @hookform/resolvers, custom form state, uncontrolled inputs |
| Tables | `@tanstack/react-table` + MiHCM `DataTable`/`Table` | 8.x | Custom sorting/filtering/pagination logic |
| Long lists (100+ items) | `@tanstack/react-virtual` | 3.x | Rendering all items, custom virtual scroll |
| i18n | `next-intl` `useTranslations()` | 4.x | Hardcoded display strings, custom i18n |
| Dates | `date-fns` + `date-fns-tz` | 4.x / 3.x | moment, dayjs, raw `Date` formatting |
| Data viz | `d3` + MiHCM color tokens | 7.x | Chart.js, Recharts, custom SVG |
| Drag & drop | `@dnd-kit/core` | 6.x | react-beautiful-dnd, custom drag handlers |
| Command palette | `cmdk` + MiHCM `Command` | 1.x | Custom search dialogs |
| Rich text | MiHCM `RichTextEditor` (wraps Lexical) | 0.x | Quill, Slate, tiptap, textarea for rich content |
| Animation | `motion` + MiHCM motion tokens | 12.x | Raw CSS transitions, react-spring, GSAP |
| File upload | `react-dropzone` | 15.x | Custom drag event handlers |
| Error monitoring | `@sentry/nextjs` | 10.x | console.error in production, custom error services |
| Analytics | `posthog-js` | 1.x | Custom event tracking, Google Analytics |
| Unit/integration tests | `vitest` + `@testing-library/react` | 4.x / 16.x | Jest (unless already configured), Enzyme |
| E2E tests | `playwright` | 1.x | Cypress, Puppeteer |
| URL state | `nuqs` `useQueryState()` / `useQueryStates()` | 2.x | `useState` + `useSearchParams` + manual URL sync |
| API client | `openapi-fetch` `createClient<paths>()` + `openapi-typescript` (dev, generates types) | 0.x / 7.x | Manual fetch wrappers, hand-typed request/response types |
| Emails | `@react-email` | 4.x | Raw HTML email strings |
| UI components | `@yashiel/mihcm-ui` | latest | shadcn/ui, Radix direct, MUI, Chakra, Ant, Mantine — all banned |

Usage detail and code examples per library: `docs/CONVENTIONS.md`.

### MiHCM-First Rule (EVERY UI element)
- Every visible element MUST be a MiHCM component or composition. Never raw `<button>`, `<input>`, `<table>`, `<dialog>` when a MiHCM component exists. Never build a UI primitive from scratch.
- Check MiHCM MCP (`mihcm_search_components`) before writing any JSX.
- **Decision order**: MiHCM component directly → compose MiHCM components → extend with wrapper → **request new component** (never build).
- **Component Request Protocol**: no MiHCM component fits and compose/extend won't work → do NOT build custom. Copy `docs/component-requests/TEMPLATE.md` to `docs/component-requests/[ComponentName].md` for the design team. Use MiHCM `Card`/`Alert` "Component pending" placeholder meanwhile. Track requests in `tasks/todo.md` under "Component Requests".
- **Imports**: subpath only — `import { Button } from '@yashiel/mihcm-ui/Button'`. Never barrel `from '@yashiel/mihcm-ui'`.

### Also Banned
- Hardcoded CSS values (hex colors, px values, raw spacing) — MiHCM tokens and Tailwind semantic classes only
- Icons outside `@yashiel/mihcm-icons`

## MCP Servers (connected tools)

| Server | Purpose | Auto-load when |
|--------|---------|---------------|
| **MiHCM** | Component search, token lookup, snippet review, contract validation, RSC boundary check | ANY UI/design work (ALWAYS) |
| **Figma** | Design-to-code context, screenshots, diagrams | Implementing designs |
| **Vercel** | Deployments, toolbar threads | Deploy, preview, production issues |
| **Context7** | Up-to-date docs for any library | ANY library/framework question |

**MCP-First Rule**: connected MCP tool can handle it → use it. MiHCM MCP > guessing. Context7 > training data. Figma > imagination. **MiHCM MCP is MANDATORY for all design/UI work.** Setup: `docs/runbooks/mcp-setup.md`.

## Skill Auto-Load (MANDATORY)
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
| MiHCM components | MiHCM MCP: `mihcm_search_components`, `mihcm_get_component` |
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

## Structure
```
src/
  web/                     → MiHCM Next.js project
    app/                   → App Router: (auth)/ (dashboard)/ api/ + layout, page, loading, error, not-found
    components/ds/         → MiHCM CLI-copied components · components/features/ → domain composites
    lib/                   → api/ (openapi-fetch client + domain modules, all server-only), auth/, schemas/ (zod, shared), env.ts, errors.ts, utils.ts
    actions/               → Server Actions by domain · hooks/ · stores/ (Zustand by domain) · providers/ (QueryClient, Theme, NuqsAdapter) · types/
  mobile/                  → Expo: app/ (Expo Router), components/, hooks/, stores/, types/
  static/                  → Plain HTML/CSS/JS: pages/, css/ (MiHCM tokens via CSS vars), js/, assets/
tasks/                     → todo.md, gotchas.md
docs/                      → ARCHITECTURE, CONVENTIONS, SECURITY, security-playbook, decisions/, runbooks/, diagrams/, component-requests/
```

## BFF Architecture (ENFORCED)
Browser NEVER calls backend APIs directly. All upstream calls go through Next.js (RSC / Server Action / Route Handler). Next.js holds tokens, aggregates, returns page-shaped data.
`Browser (cookie) → Next.js → MIHCM APIs`
- Auth: OIDC Authorization Code + PKCE against IdentityServer4 (Entra ID as corporate IdP behind it). Next.js is the confidential client — access/refresh tokens stay server-side; browser holds only the httpOnly session cookie. Upstream MIHCM API calls attach the bearer access token server-side.
- Every `lib/api/*` file MUST start with `import 'server-only'`
- All upstream fetches use `openapi-fetch` client → domain modules → pages. Never call the client from a page directly.
- Generate types: `npx openapi-typescript ./specs/api.yaml -o ./src/web/lib/api/v1.d.ts` · client via `createClient<paths>()` in `lib/api/client.ts`
- Upstream tokens live in server-only env vars — NEVER `NEXT_PUBLIC_` for secrets

## Data Fetching Rules
**Rule 1 — Parallelize** (sequential awaits = bug): `const [a, b] = await Promise.all([getFoo(), getBar()]);`
**Rule 2 — Stream** — wrap slow components in `<Suspense fallback={<Skeleton />}>`. Skeleton MUST match component dimensions (prevents layout shift).
**Rule 3 — Deduplicate** — wrap fetch functions in `cache()` so multiple components share one upstream call.

**Caching** (always set `next: { revalidate, tags }`):
| Data | revalidate |
|------|-----------|
| Profile, org chart | 300s |
| Leave balance | 60s |
| Attendance today | 30s |
| Notifications / real-time | 0 — use TanStack Query |
| Reference data (depts, roles) | 3600s |

Invalidate precisely: `revalidateTag('employees:list')` — never `revalidatePath('/')`.
**TanStack Query staleTimes**: profile `5min` · leave `60s` · attendance/notifications `0`
**RSC → Client hydration** (interactive pages, avoids double-fetch): `prefetchQuery` on server → `HydrationBoundary state={dehydrate(qc)}`.
**Aggregation** (multi-source pages): `Promise.allSettled` — one failed call doesn't kill the page; return `null` for failed segments.
**Trim payloads**: return only fields the component needs. Never pass full upstream objects to the client.

## Mutations (Server Actions First)
Prefer Server Actions over Route Handlers for forms. Sequence: `requireSession()` → `Schema.safeParse()` → domain API call (`lib/api/*`) → `revalidateTag()` → `redirect()` (OUTSIDE try/catch).
Route Handlers only for: external webhooks, mobile apps, third-party consumers.
Errors: log full detail server-side, return generic message to client. Never expose stack traces or upstream error messages.

## Decision Trees
**Where to fetch?** On page load → RSC + `revalidate` + tags. Interactive/paginated/polled → TanStack Query (optionally prefetch with `HydrationBoundary`).
**Server Action vs Route Handler?** Form in this app → Server Action. External / mobile / webhook → Route Handler.
**`'use client'`?** Needs state, effects, event handlers, or browser API → `'use client'` (push directive as deep as possible). Otherwise → RSC.
**URL state or React state?** Filterable/sortable/paginated/shareable → `nuqs`. Ephemeral UI-only (modals, tooltips) → Zustand or `useState`.

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
Update when architecture changes, in the SAME commit. ERD + Sequence = highest priority. Table of triggers: `docs/ARCHITECTURE.md`.

## Docs (read on demand — do NOT assume contents, open the file)
- **docs/AGENTS.md** — read before dispatching subagents: roster, chains, dispatch rules
- **docs/ARCHITECTURE.md** — read for system design, data flow, deployment, diagram triggers
- **docs/CONVENTIONS.md** — read before writing code: naming, RSC rules, per-library usage + examples, MiHCM component catalog + tokens, design standards, accessibility, 8-lens detail
- **docs/security-playbook.md** — read during security review or auth/payment/PII/upload/webhook work: 100+ rules (AUTH/INJ/AUTHZ/...)
- **docs/runbooks/mcp-setup.md** — read when configuring MCP servers
- @docs/SECURITY.md

## Publishing (Dual-Repo)
1. **`{project-name}`** — everything (CLAUDE.md, docs/, tasks/, src/, configs)
2. **`{project-name}-app`** — deployable only (src/, configs, package.json)

## Compaction
Preserve: modified files, failing tests, branch + task context, todo/gotchas contents, agent chain state, MiHCM component context
