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

| Layer | Technology | Notes |
|-------|-----------|-------|
| Framework | Next.js 15+ (App Router, React 19) | TypeScript strict mode |
| Validation | Zod | All input boundaries |
| State (client) | Zustand | Client-side state only |
| Data fetching (client) | TanStack Query | Client polling/optimistic only |
| Styling | Tailwind CSS 4 | Semantic tokens only, no hardcoded values |
| Design System | MiHCM Design System (`@yashiel/mihcm-ui`) | ALL UI components from here |
| Icons | MiHCM Icons (`@yashiel/mihcm-icons`) | 1,703 Lucide-based icons |
| Tokens | MiHCM Tokens (`@yashiel/mihcm-tokens`) | CSS variables + TS constants |
| Theme | MiHCM Theme (`@yashiel/mihcm-theme`) | Tailwind 4 preset + globals.css |
| AI UI | MiHCM AI UI (`@yashiel/mihcm-ai-ui`) | Zod-validated generative UI |
| CLI | MiHCM CLI (`@yashiel/mihcm-cli`) | `npx @yashiel/mihcm-cli add <Component>` |
| Deployment | Vercel | Git-push deploys |

### Banned
- shadcn/ui — DO NOT use. Not installed. Not allowed.
- Radix UI (direct) — use MiHCM wrappers instead (they wrap Radix internally)
- Material UI, Chakra UI, Ant Design, Mantine, or any other component library
- Hardcoded CSS values (hex colors, px values, raw spacing) — use MiHCM tokens and Tailwind semantic classes
- Any UI primitive not from `@yashiel/mihcm-ui`

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
    lib/                   → env.ts (zod), errors.ts, utils.ts (cn), [backend client]
    actions/               → Server Actions by domain
    hooks/                 → use-[name].ts
    stores/                → Zustand stores by domain
    providers/             → QueryClientProvider, ThemeProvider
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
docs/                      → ARCHITECTURE, CONVENTIONS, SECURITY, security-playbook, decisions/, runbooks/, diagrams/
```

## Data Fetching
Server Components → reads · Server Actions → writes (Zod validation, `revalidatePath`) · Route Handlers → webhooks/external APIs · TanStack Query → client polling/optimistic · Zustand → client UI state · `Promise.all` for parallel · `<Suspense>` to stream

## Commands
```bash
npm run dev · npm run build · npm run lint · npm test · npx tsc --noEmit · npx @yashiel/mihcm-cli add <Component>
```

## Quality Gate (every change)
- **Karpathy**: think first? simple? surgical? verified?
- **MiHCM**: ALL components from `@yashiel/mihcm-ui`? No hardcoded values?
- **Verification**: 6-phase loop passed?
- **Code**: no dead code. Functions <50 lines. Immutable. See `docs/CONVENTIONS.md`.
- **Security**: `docs/security-playbook.md` rules. See `docs/SECURITY.md`.
- **Accessibility**: WCAG 2.1 AA. See `docs/CONVENTIONS.md`.
- **Search Audit**: checked MiHCM MCP and packages before custom code?

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
