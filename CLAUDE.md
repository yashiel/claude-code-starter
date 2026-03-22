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

## Thinking Mindset
On EVERY task, think through ALL five lenses before writing a single line of code:

| Role | What You Ask |
|------|-------------|
| **System Architect** | Does this fit the system? Data flow? Boundaries? Scale to 10x/100x? |
| **Software Engineer** | Clean code? SOLID? Error handling? Edge cases? Type safety? |
| **Database Engineer** | Schema correct? Indexes? RLS? Queries efficient? Migrations reversible? |
| **Frontend Developer** | RSC boundaries? Accessible? Mobile-first? Loading/error states? Performance? |
| **QA Engineer** | What breaks this? Null? Empty? 10k items? Unicode? Concurrent users? Auth bypass? |

This is not optional. Every feature, every fix, every refactor — run all five lenses.

## Principles
- **Simplicity First** — minimal code, clean solutions
- **No Lazy Fixes** — root cause, not symptoms. Senior standards.
- **Minimal Impact** — touch only what's necessary
- **Systems > Prompts** — build reusable skills/hooks/utils
- **Verification > Generation** — proving > writing
- **Iteration > Perfection** — ship, learn, update `tasks/gotchas.md`
- **Plan Before Code** — ALWAYS enter planning mode first. No exceptions. Think architecturally.
- **Skills Are Mandatory** — if a skill exists for the task, USE IT. No rationalising skips.
- **Research First** — use Context7, WebSearch, Firecrawl, and MCP docs to get current information. Never rely on stale training data when live docs exist.
- **Memory Is Continuous** — update `MEMORY.md` at end of every session. Next session starts by reading it.

## Workflow
1. **Plan Mode ALWAYS** — plan ANY 2+ step task. Enter `EnterPlanMode` before touching code. STOP and re-plan if sideways. Write specs upfront. Think architecturally through all 5 role lenses.
2. **Research Before Building** — use Context7/WebSearch/Firecrawl to read latest docs, APIs, and patterns BEFORE implementing. Never guess when you can look it up.
3. **Skills First** — check available skills before ANY action. If a skill applies (even 1% chance), invoke it. Use `brainstorm` skill before creative work. Use `plan` skill for planning. Use domain skills (shadcn, next-best-practices, owasp-security, etc.) for implementation.
4. **Subagents** — split research/execution/analysis. One task per agent. Parallelise independent work.
5. **Self-Improvement** — log mistakes → `tasks/gotchas.md` → convert to rules → review at session start.
6. **Verify Before Done** — never mark done without proof. Run tests, check types, lint. "Would a staff engineer approve this?"
7. **Demand Elegance** — non-trivial: "is there a cleaner way?" Simple: don't over-engineer.
8. **Autonomous Bugs** — just fix it. Root cause, not symptoms. No hand-holding. Use `systematic-debugging` skill.
9. **Skills = System Layer** — code + scripts + data, not just markdown.
10. **File System = Context** — `references/`, `scripts/`, `templates/` for progressive disclosure.
11. **Don't Over-Constrain** — context > control. Flexibility > rigid steps.
12. **Memory Discipline** — update memory at session end. Read memory at session start. Context must survive across sessions.

## Session Protocol

### Session Start (MANDATORY)
1. Read `MEMORY.md` — recall what was done in previous sessions
2. Read `tasks/todo.md` — check pending work
3. Read `tasks/gotchas.md` — avoid repeating mistakes
4. Run `/catchup` skill if available
5. Announce context: "Resuming from [last session summary]. Pending: [items]."

### Session End (MANDATORY)
1. Update `tasks/todo.md` — mark completed, add new items
2. Update `tasks/gotchas.md` — capture any lessons learned
3. Update `MEMORY.md` — write session summary with:
   - What was built/changed (files, features, fixes)
   - Decisions made and why
   - What's next / blockers
   - Any architectural context future sessions need
4. Commit memory updates

### Error Handling Protocol
When encountering ANY error:
1. **Don't panic-fix** — read the full error, understand root cause
2. **Use `systematic-debugging` skill** if available
3. **Check all 5 role lenses** — is this a schema issue? Auth issue? Frontend state? Race condition?
4. **Research** — use Context7/WebSearch for the specific error before guessing
5. **Fix architecturally** — not a band-aid. Would this fix survive 100x traffic?
6. **Log lesson** → `tasks/gotchas.md`

## Task Flow
1. **Research** — read relevant docs (Context7, web, MCP docs) before planning
2. **Plan** → `tasks/todo.md` with checkable items. Use `EnterPlanMode`. Think through all 5 lenses.
3. **Invoke Skills** — load all applicable skills before implementation
4. Verify plan before coding
5. Track progress as you go (TodoWrite)
6. Explain changes at each step
7. Document results in `tasks/todo.md`
8. Capture lessons → `tasks/gotchas.md`
9. **Update `MEMORY.md`** — session summary for continuity

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
