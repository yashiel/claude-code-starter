# [PROJECT_NAME]

> [One-line description]

## Stack
Next.js 15+ (App Router, React 19) · TypeScript strict · shadcn/ui · Tailwind CSS 4 · Appwrite (Auth/DB/Storage) · TanStack Query (client polling only) · Stripe + PayPal + TNG eWallet · Vercel or Appwrite Sites

## MCP Servers (connected tools)
- **Appwrite API** — manage databases, users, storage, functions directly from Claude (via `mcp-server-appwrite`)
- **Appwrite Docs** — access latest Appwrite documentation in real-time
- **Figma** — `get_design_context` for design-to-code, `get_screenshot`, `generate_diagram` (FigJam)
- **Vercel** — deployments, toolbar threads
- **Sentry** — error monitoring and debugging
- **Context7** — up-to-date docs for any library (Next.js, React, Appwrite SDK)
- **Gmail** — email drafts and labels
- **Canva** — graphics and marketing visuals
- **Indeed** — job search, company data

Setup guide: `docs/runbooks/mcp-setup.md`

**MCP-First Rule**: When a connected MCP tool can handle the task, use it. Appwrite MCP for DB operations > writing SDK code manually. Figma MCP for design context > guessing from descriptions. Context7 for latest docs > relying on training data.

## Principles
- **Simplicity First** — minimal code, clean solutions
- **No Lazy Fixes** — root cause, not symptoms. Senior standards.
- **Minimal Impact** — touch only what's necessary
- **Systems > Prompts** — build reusable skills/hooks/utils
- **Verification > Generation** — proving > writing
- **Iteration > Perfection** — ship, learn, update `tasks/gotchas.md`

## Workflow
1. **Plan Mode** — plan ANY 3+ step task. STOP and re-plan if sideways. Write specs upfront.
2. **Subagents** — split research/execution/analysis. One task per agent.
3. **Self-Improvement** — log mistakes → `tasks/gotchas.md` → convert to rules → review at session start.
4. **Verify Before Done** — never mark done without proof. "Would a staff engineer approve this?"
5. **Demand Elegance** — non-trivial: "is there a cleaner way?" Simple: don't over-engineer.
6. **Autonomous Bugs** — just fix it. Root cause, not symptoms. No hand-holding.
7. **Skills = System Layer** — code + scripts + data, not just markdown.
8. **File System = Context** — `references/`, `scripts/`, `templates/` for progressive disclosure.
9. **Don't Over-Constrain** — context > control. Flexibility > rigid steps.

## Task Flow
1. Plan → `tasks/todo.md` with checkable items
2. Verify plan before coding
3. Track progress as you go
4. Explain changes at each step
5. Document results in `tasks/todo.md`
6. Capture lessons → `tasks/gotchas.md`

## Commands
```bash
npm run dev · npm run build · npm run lint · npm test · npx tsc --noEmit · npx shadcn@latest add
```

## Structure
```
src/app/           → App Router (layout, page, loading, error, not-found, global-error)
  (auth)/          → login, register
  (dashboard)/     → protected routes
  api/             → webhooks only (Stripe, PayPal, TNG)
src/components/ui/ → shadcn (CLI-managed, never create manually)
src/components/features/ → domain composites from ui/
src/lib/appwrite/  → server.ts, client.ts, config.ts, types.ts
src/lib/payments/  → stripe/, paypal/, tng/, router.ts, types.ts
src/lib/           → env.ts (zod), errors.ts, utils.ts (cn)
src/actions/       → Server Actions by domain
src/hooks/         → use-[name].ts
src/providers/     → QueryClientProvider, ThemeProvider
src/types/         → shared types
tasks/             → todo.md, gotchas.md
tools/scripts/     → automation scripts
tools/prompts/     → reusable prompt templates
docs/              → ARCHITECTURE, CONVENTIONS, SECURITY, PAYMENTS, security-playbook, decisions/, runbooks/
```

## Data Fetching
Server Components → reads (node-appwrite or Appwrite MCP) · Server Actions → writes (zod + Appwrite) · Route Handlers → webhooks only · TanStack Query → client polling/optimistic only · `Promise.all` for parallel · `<Suspense>` to stream · `React.cache()` for dedup

## Rules

**Appwrite**: Two SDKs never mixed (`node-appwrite` server, `appwrite` browser). Auth: Server Action → httpOnly cookie → middleware → `createSessionClient()`. All IDs in `config.ts`. Serialise dates before client. Use Appwrite MCP for DB schema operations and exploration; use SDK in application code.

**Figma**: Use Figma MCP `get_design_context` as primary tool for design-to-code. Use `implement-design` + `create-design-system-rules` skills alongside. Use `generate_diagram` for FigJam flowcharts/sequences.

**shadcn**: CLI only (`npx shadcn@latest add`). Semantic tokens only (`bg-primary` not `bg-blue-500`). `gap-*` not `space-*`. `size-*` for equal dims. Forms: `FieldGroup`+`Field`. Icons: `data-icon`. Run `docs` command before using.

**React**: No async client components. Serialisable props only across RSC boundary. Composition > booleans. `handleVerbNoun`. Avoid barrel files.

**Payments**: See `docs/PAYMENTS.md`. Stripe Checkout Sessions. PayPal Orders v2 + JS SDK. TNG server-to-server RSA256, amount STRING in cents, handle `U` status.

## Quality Gate (every change)
- **Code**: remove dead code/duplicates/unused. Beginner-readable. No magic values.
- **Security**: apply `docs/security-playbook.md`. Reference rule IDs (AUTH-02, INJ-01). Pre-Commit Checklist §22.
- **Tests**: run all. Worst-case + best-case. Adversarial: null, empty, 10k, Unicode, SQL.
- **Architecture**: plan first. Design for 10x/100x.
- **UX**: test interactions. Mobile-first. WCAG 2.1 AA. Skeletons > spinners. Error boundaries.

## Warnings
- Never `node-appwrite` in `"use client"` · Never create UI primitives manually · `lib/env.ts` not `process.env` · `redirect()` OUTSIDE try/catch · Serialise Appwrite dates · `error.tsx` must be `"use client"` · All payment keys server-only · TNG: STRING cents, `U` ≠ failed · Semantic tokens only · Use Appwrite MCP for DB exploration, SDK for app code

## Docs
- **@docs/ARCHITECTURE.md** — system design, data flow, MCP integration points
- **@docs/CONVENTIONS.md** — naming, RSC, shadcn, payments
- **@docs/security-playbook.md** — 22 sections, 100+ rules (AUTH/INJ/AUTHZ/PAY/AW), master checklists
- **@docs/SECURITY.md** — 24-rule quick-ref with playbook cross-refs
- **@docs/PAYMENTS.md** — Stripe, PayPal, TNG full API reference
- **@docs/runbooks/mcp-setup.md** — MCP server setup commands and troubleshooting
- **@docs/runbooks/** — operational procedures
- **@docs/decisions/** — ADRs

## Skills
Next.js: next-best-practices, next-cache-components, next-upgrade · React: vercel-react-best-practices, vercel-composition-patterns · UI: shadcn, frontend-design, design-auditor, web-design-guidelines, implement-design, create-design-system-rules · Security: owasp-security, vibesec-skill, sanitize, ffuf-web-fuzzing · Payments: stripe-best-practices · Deploy: deploy-to-vercel · Research: firecrawl suite · Testing: webapp-testing, agent-browser · Data: database-schema-designer · Plugins: design, brand-voice

## Compaction
Preserve: modified files list, failing tests, branch + task context, `tasks/todo.md` + `tasks/gotchas.md` contents, active Appwrite collections
