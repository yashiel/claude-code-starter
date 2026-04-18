# [PROJECT_NAME]

> [One-line description]

---

## Core Behavioral Rules (Priority 1)

Behavioral guidelines to reduce common LLM coding mistakes. Bias toward caution over speed. For trivial tasks, use judgment.

### 1. Think Before Coding
**Don't assume. Don't hide confusion. Surface tradeoffs.**

- State assumptions explicitly. If uncertain, ask.
- Multiple interpretations? Present them — don't pick silently.
- Simpler approach? Say so. Push back when warranted.
- Unclear? Stop. Name what's confusing. Ask.

### 2. Simplicity First
**Minimum code that solves the problem. Nothing speculative.**

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility"/"configurability" not requested.
- No error handling for impossible scenarios.
- 200 lines when 50 would do? Rewrite.

Ask: "Would a senior engineer call this overcomplicated?" If yes, simplify.

### 3. Surgical Changes
**Touch only what you must. Clean up only your own mess.**

- Don't "improve" adjacent code, comments, formatting.
- Don't refactor what isn't broken.
- Match existing style even if you'd do it differently.
- Noticed unrelated dead code? Mention, don't delete.
- Remove imports/vars/functions YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

Test: every changed line traces directly to user request.

### 4. Goal-Driven Execution
**Define success criteria. Loop until verified.**

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, make pass"
- "Fix bug" → "Write test reproducing it, make pass"
- "Refactor X" → "Tests pass before and after"

Multi-step tasks — state brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
```

Strong criteria = loop independently. Weak criteria ("make it work") = constant clarification.

**Working if**: fewer unnecessary diffs, fewer rewrites from overcomplication, clarifying questions come BEFORE implementation.

---

## Stack
Next.js 15+ (App Router, React 19) · TypeScript strict · shadcn/ui · Tailwind CSS 4 · Lucide Icons · TanStack Query (client polling only) · Vercel

## MCP Servers
- **Figma** — design context, screenshots
- **Vercel** — deployments
- **Sentry** — error monitoring
- **Context7** — up-to-date library docs

Rule: MCP tool available for task → use it over guessing. Setup: `docs/runbooks/mcp-setup.md`.

## Principles
- **Plan before code** — any 2+ step task.
- **Search first** — Adopt > Extend > Compose > Build. Never reinvent.
- **Research first** — Context7/WebSearch over stale training data.
- **Skills first** — skill exists for task → use it.
- **Immutability** — new objects, pure functions.
- **Memory continuous** — read `MEMORY.md` at start, update at end.

## Workflow
1. Plan (read Core Rules §1, §4)
2. Search existing packages/MCP tools/skills
3. Research latest docs if unsure
4. Build (Core Rules §2, §3)
5. Verify (loop below)
6. Update `MEMORY.md` + `tasks/gotchas.md`

## Verification Loop (before marking done)

| Phase | Command |
|-------|---------|
| Build | `npm run build` |
| TypeCheck | `npx tsc --noEmit` |
| Lint | `npm run lint` |
| Tests | `npm test` |
| Security | Apply `docs/security-playbook.md` for auth/payment/PII |
| Diff | `git diff` — review all changes |

Any phase fails → task NOT done. Fix, re-verify.

## Design Rules (UI work only)
- WCAG 2.1 AA minimum. Keyboard nav. 4.5:1 contrast. Touch targets 44px+.
- Semantic tokens (`bg-primary`), not raw colors.
- Mobile-first (375px first).
- Every interactive element: default/hover/focus/active/disabled/loading/error states.
- Skeletons, not spinners.
- Research 3-5 real products (Linear, Vercel, Stripe, Notion) before designing.

## Code Quality
- Functions <50 lines. Files <400 lines. Nesting ≤4.
- `interface` for extendable shapes, `type` for unions. Never `any` → `unknown` + narrow.
- `redirect()`/`notFound()` OUTSIDE try/catch.
- Custom `AppError` from `lib/errors.ts`. Fail closed.
- Validate inputs server-side with Zod.

## RSC Rules
- Server Components default. `"use client"` only for hooks/events/browser APIs.
- No async client components.
- Serialisable props only across RSC boundary.
- Server Components → reads. Server Actions → writes (zod + `revalidatePath`). Route Handlers → webhooks. TanStack Query → client polling only.

## shadcn
CLI only: `npx shadcn@latest add`. Semantic tokens. `gap-*` not `space-*`. `size-*` for equal dims.

## Structure
```
src/app/           → App Router (layout, page, loading, error)
src/components/ui/ → shadcn primitives (CLI-managed)
src/components/features/ → domain composites
src/lib/           → env.ts (zod), errors.ts, utils.ts
src/actions/       → Server Actions by domain
tasks/             → todo.md, gotchas.md
docs/              → ARCHITECTURE, CONVENTIONS, SECURITY, security-playbook, diagrams/
```

## Memory
Session start: read `MEMORY.md` → `tasks/todo.md` → `tasks/gotchas.md`.
Session end: update all three.
Mistakes → `tasks/gotchas.md` → promote to rule if repeated.

## Commands
```bash
npm run dev · npm run build · npm run lint · npm test · npx tsc --noEmit
```

## Docs
- `@docs/ARCHITECTURE.md` — system design, data flow, diagrams
- `@docs/CONVENTIONS.md` — naming, RSC, code quality, design standards
- `@docs/security-playbook.md` — 100+ rules (reference IDs in reviews: "Violates AUTH-02")
- `@docs/SECURITY.md` — 24-rule quick ref
- `@docs/AGENTS.md` — multi-agent orchestration
- `@docs/runbooks/` — MCP setup, operational procedures
- `@docs/decisions/` — ADRs

## Warnings
- Never create UI primitives manually (use shadcn CLI).
- `lib/env.ts`, not raw `process.env`.
- `redirect()` OUTSIDE try/catch.
- `error.tsx` must be `"use client"`.
- Semantic tokens only.
