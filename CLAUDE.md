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
On EVERY task, think through ALL eight lenses before writing a single line of code:

### Engineering Lenses
| Role | What You Ask |
|------|-------------|
| **System Architect** | Does this fit the system? Data flow? Boundaries? Scale to 10x/100x? |
| **Software Engineer** | Clean code? SOLID? Error handling? Edge cases? Type safety? |
| **Database Engineer** | Schema correct? Indexes? RLS? Queries efficient? Migrations reversible? |
| **QA Engineer** | What breaks this? Null? Empty? 10k items? Unicode? Concurrent users? Auth bypass? |

### Design Lenses (MANDATORY for any UI/UX work)
| Role | What You Ask |
|------|-------------|
| **Designer** | Is this production-grade? Would a real design team ship this? No AI slop — every pixel intentional. Research real products (Dribbble, Awwwards, Apple HIG, Material 3) before designing. Use `frontend-design`, `refactoring-ui`, `top-design` skills. |
| **Creative Director** | Does this have a cohesive visual identity? Typography hierarchy? Color story? Consistent spacing rhythm? Does it feel crafted or generated? Would this win design awards? |
| **UX Designer** | Is the user journey frictionless? Cognitive load minimized? Information architecture clear? Feedback for every action? Error recovery graceful? Progressive disclosure? Use `ux-heuristics`, `design-everyday-things`, `microinteractions` skills. |
| **Frontend Developer** | RSC boundaries? Accessible? Mobile-first? Loading/error states? Performance? Animations purposeful (not decorative)? Touch targets 44px+? |

This is not optional. Every feature, every fix, every refactor — run all eight lenses.

## Design Philosophy (ZERO AI SLOP)
Every UI element must pass these gates. If it looks like AI generated it, it fails.

### Non-Negotiable Standards
1. **Accessibility is #1** — WCAG 2.1 AA minimum. Keyboard navigable. Screen reader tested. Color contrast 4.5:1+. Focus indicators visible. `aria-*` attributes correct. Skip links. Reduced motion support.
2. **Aesthetics are #1** — Typography with purpose (max 2 typefaces, clear hierarchy). Intentional whitespace. Consistent spacing scale (4px/8px base). Color palette with meaning (not random). Depth through subtle shadows, not flat or over-shadowed.
3. **Design Philosophies are #1** — Apply real design principles: Gestalt (proximity, similarity, closure), Fitts's Law (target size + distance), Hick's Law (fewer choices = faster decisions), Miller's Law (7±2 chunks), Jakob's Law (users expect your site to work like others they know).

### Before Building ANY UI
1. **Research** — look at 3-5 real production examples of similar UI (WebSearch/Firecrawl). Study how Apple, Linear, Vercel, Stripe, Notion design similar interfaces.
2. **Invoke design skills** — `frontend-design`, `refactoring-ui`, `top-design`, `web-design-guidelines`, `design-auditor`, `shadcn`. Always.
3. **Plan the design system** — spacing scale, color tokens, typography scale, component inventory. Don't ad-hoc it.
4. **Mobile-first** — design for 375px first, then scale up. Not the other way around.
5. **Interaction design** — every interactive element needs: default, hover, focus, active, disabled, loading states. Use `microinteractions` skill.

### AI Slop Detection Checklist (run on every UI change)
- [ ] Does this look like a real product or a demo/tutorial?
- [ ] Would a design-conscious user trust this with their credit card?
- [ ] Is there a clear visual hierarchy (one primary action per view)?
- [ ] Are transitions/animations purposeful or just decorative?
- [ ] Is the copy real (not "Lorem ipsum" or "Click here")?
- [ ] Does every icon have a text label or aria-label?
- [ ] Is the empty state designed (not just "No data")?
- [ ] Are error messages helpful and human ("We couldn't load your orders" not "Error 500")?
- [ ] Is the loading state a skeleton, not a spinner?
- [ ] Does it work without JavaScript? (progressive enhancement)
- [ ] Would Dieter Rams approve? ("Less, but better")

## Principles
- **Simplicity First** — minimal code, clean solutions
- **No Lazy Fixes** — root cause, not symptoms. Senior standards.
- **Minimal Impact** — touch only what's necessary
- **Systems > Prompts** — build reusable skills/hooks/utils
- **Verification > Generation** — proving > writing
- **Iteration > Perfection** — ship, learn, update `tasks/gotchas.md`
- **Plan Before Code** — ALWAYS enter planning mode first. No exceptions. Think architecturally.
- **Skills Are Mandatory** — if a skill exists for the task, USE IT. No rationalising skips.
- **Search First** — before writing ANY new code: search for existing packages, MCP tools, skills, and patterns. Decision matrix: Adopt (use directly) > Extend (install + thin wrapper) > Compose (combine packages) > Build (custom, informed by research). Never reinvent what exists.
- **Research First** — use Context7, WebSearch, Firecrawl, and MCP docs to get current information. Never rely on stale training data when live docs exist.
- **Immutability** — always create new objects, never mutate existing ones. Pure functions over side effects.
- **Hooks Over Prompts** — deterministic enforcement via hooks > probabilistic prompt instructions. Use scripts for calendar math, file validation, timezone handling — not the LLM.
- **Memory Is Continuous** — update `MEMORY.md` at end of every session. Next session starts by reading it.
- **Observations Evolve** — mistakes → gotchas → rules → skills. Confidence-weighted: tentative (0.3) → probable (0.6) → near-certain (0.9).

## Workflow
1. **Plan Mode ALWAYS** — plan ANY 2+ step task. Enter `EnterPlanMode` before touching code. STOP and re-plan if sideways. Write specs upfront. Think architecturally through all 8 lenses.
2. **Search First** — before writing new code, search for existing packages/MCP tools/skills/patterns. Adopt > Extend > Compose > Build.
3. **Research Before Building** — use Context7/WebSearch/Firecrawl to read latest docs, APIs, and patterns BEFORE implementing. Never guess when you can look it up.
4. **Skills First** — check available skills before ANY action. If a skill applies (even 1% chance), invoke it. Use `brainstorm` skill before creative work. Use `plan` skill for planning. Use domain skills (shadcn, next-best-practices, owasp-security, etc.) for implementation.
5. **Agent Orchestration** — dispatch specialised subagents for complex work. See `docs/AGENTS.md` for full patterns. Orchestration chains by task type:
   - **Feature**: planner → researcher → implementer → code-reviewer → security-reviewer
   - **Bugfix**: debugger → implementer → code-reviewer
   - **Refactor**: architect → code-reviewer → implementer
   - **UI/Design**: design researcher → implementer → design-auditor → a11y-reviewer
6. **Verify Before Done** — run the 6-phase verification loop: Build → TypeCheck → Lint → Tests → Security Scan → Diff Review. Never mark done without all phases passing.
7. **Self-Improvement** — log mistakes → `tasks/gotchas.md` → convert to rules → review at session start. Observations evolve: gotcha (0.3) → pattern (0.6) → rule (0.9).
8. **Demand Elegance** — non-trivial: "is there a cleaner way?" Simple: don't over-engineer.
9. **Autonomous Bugs** — just fix it. Root cause, not symptoms. No hand-holding. Use `systematic-debugging` skill.
10. **Skills = System Layer** — code + scripts + data, not just markdown.
11. **File System = Context** — `references/`, `scripts/`, `templates/` for progressive disclosure.
12. **Don't Over-Constrain** — context > control. Flexibility > rigid steps.
13. **Memory Discipline** — update memory at session end. Read memory at session start. Context must survive across sessions.

## Verification Loop (run before ANY completion claim)
Every task must pass all 6 phases before claiming "done":

| Phase | Command | Fail Action |
|-------|---------|-------------|
| 1. Build | `npm run build` | Fix build errors, re-run |
| 2. TypeCheck | `npx tsc --noEmit` | Fix type errors, re-run |
| 3. Lint | `npm run lint` | Fix lint issues, re-run |
| 4. Tests | `npm test` | Fix failing tests, re-run |
| 5. Security | Run `owasp-security` / `vibesec-skill` | Fix CRITICAL issues immediately |
| 6. Diff Review | `git diff` — review all changes | Remove dead code, check for secrets, verify intent |

**Output**: Structured VERIFICATION REPORT — READY or NOT READY with specific failures listed.
**Rule**: If ANY phase fails, the task is NOT done. Fix and re-verify. No exceptions.

## Persona Protocol (runs on EVERY task)

Before writing ANY code: state task → run all 8 personas → identify conflicts → resolve → proceed.

### Persona Checklist (copy into every plan)
- [ ] **System Architect** — fits system? data flow? boundaries? scale 10x/100x?
- [ ] **Software Engineer** — clean? SOLID? error handling? edge cases? types?
- [ ] **Database Engineer** — schema? indexes? RLS? queries? migrations reversible?
- [ ] **QA Engineer** — what breaks? null? empty? 10k? unicode? concurrent? auth bypass?
- [ ] **Designer** — production-grade? researched 3-5 real examples? no AI slop?
- [ ] **Creative Director** — visual identity? typography hierarchy? color story? spacing rhythm?
- [ ] **UX Designer** — frictionless journey? cognitive load? feedback? error recovery? progressive disclosure?
- [ ] **Frontend Developer** — RSC boundaries? accessible? mobile-first? all states? performance?

### Persona Priority by Task
| Task | Primary | Secondary |
|------|---------|-----------|
| Feature | Architect, Engineer, QA | All |
| UI | Designer, Creative Dir, UX, Frontend | QA |
| Bug fix | QA, Engineer | Architect |
| DB change | DB Engineer, Architect | QA |
| API | Architect, Engineer, QA | Security |
| Refactor | Architect, Engineer | QA |

## Auto-Skill Loading (MANDATORY)

Skills load AUTOMATICALLY by task type. Never skip.

| Task | Skills |
|------|--------|
| ANY UI | `frontend-design`, `refactoring-ui`, `shadcn`, `ux-heuristics`, `microinteractions`, `web-design-guidelines`, `design-auditor` |
| Page/layout | Above + `top-design`, `web-typography`, `design-everyday-things` |
| Planning | `plan`, `brainstorm` |
| Bug fix | `systematic-debugging`, `fix` |
| Code review | `review`, `clean-code` |
| Auth/security | `owasp-security`, `vibesec-skill` |
| Payments | `stripe-best-practices` + `docs/PAYMENTS.md` |
| Database | `database-schema-designer` + `docs/security-playbook.md` §10 |
| React | `vercel-react-best-practices`, `vercel-composition-patterns` |
| Next.js | `next-best-practices`, `next-cache-components` |
| shadcn | `shadcn` (run `docs` first) |
| Figma | `implement-design`, `create-design-system-rules` |
| Deploy | `deploy-to-vercel` |
| Tests | `webapp-testing`, `agent-browser` |
| Research | `firecrawl-search`, `firecrawl-scrape`, `firecrawl-crawl` |
| Content | `brand-voice` |

**Rules**: 1% chance = load it. Multiple apply = load ALL. Design skills ALWAYS for UI. No rationalising skips.

## Memory System

**Session start**: read `MEMORY.md` → `tasks/todo.md` → `tasks/gotchas.md` → announce context.
**Session end**: update all three → commit.
**During**: update `tasks/todo.md` on completion, `tasks/gotchas.md` on mistakes, `MEMORY.md` on decisions.

| What | Where |
|------|-------|
| Features built, changes | `MEMORY.md` → Latest Session |
| Why X over Y | `MEMORY.md` → Architecture Decisions |
| Mistakes, gotchas | `tasks/gotchas.md` |
| Branch, state, issues | `MEMORY.md` → Active Context |
| Pending work | `tasks/todo.md` |

## Diagrams (MANDATORY — maintain in `docs/diagrams/`)

All diagrams use Mermaid format. Store in `docs/diagrams/`. Update when architecture changes.

| Diagram | File | When to Update |
|---------|------|---------------|
| ERD | `erd.md` | Any collection/table/relationship change |
| Class | `class.md` | New service, model, or major refactor |
| Deployment | `deployment.md` | Infrastructure, env, or provider change |
| Use Case | `use-cases.md` | New user-facing feature or actor |
| Sequence | `sequences.md` | New API flow, auth flow, or payment flow |
| Activity | `activities.md` | New business process or workflow |
| State Machine | `state-machines.md` | New stateful entity (order, payment, subscription) |

**Rules**:
1. Create diagrams when the feature is first built
2. Update ALL affected diagrams when making changes — not optional
3. Use Figma MCP `generate_diagram` for visual versions in FigJam when available
4. ERD and Sequence diagrams are the most critical — always keep current
5. Reference diagrams in `MEMORY.md` when architecture changes

## Error Handling Protocol
1. Don't panic-fix — read full error, understand root cause
2. Use `systematic-debugging` skill
3. Check all 8 lenses — schema? auth? frontend state? race condition? design? UX?
4. Research via Context7/WebSearch before guessing
5. Fix architecturally — would this survive 100x traffic?
6. Log lesson → `tasks/gotchas.md`

## Task Flow
1. Run Personas → Auto-Load Skills → Research → Plan (`tasks/todo.md`) → Verify plan → Build → Track (TodoWrite) → Verify (6-phase) → Lessons (`tasks/gotchas.md`) → Memory (`MEMORY.md`)

## Publishing Protocol (Dual-Repo Strategy)
When asked to "publish to GitHub" or "push to GitHub", ALWAYS create TWO repositories:

1. **`{project-name}`** — EVERYTHING (CLAUDE.md, docs/, tasks/, tools/, .claude/, src/, configs). Development workspace.
2. **`{project-name}-app`** — Deployable only: `src/ public/ package.json package-lock.json tsconfig.json next.config.ts tailwind.config.ts postcss.config.mjs components.json .env.example .gitignore README.md`. Excludes all dev docs/tools.

**Execute**: `gh repo create {name} --public --source=. --push` for repo 1. Copy deployable files to `/tmp/{name}-app`, init git, create repo 2 with same command.
**Sync**: Remind user or set up GitHub Action to auto-copy deployable files on push to main.
**Why**: Cloud providers only need the app. Dev context stays separate.

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
docs/diagrams/     → ERD, class, deployment, use-case, sequence, activity, state-machine (Mermaid)
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
- **Verification**: run full 6-phase Verification Loop (Build → TypeCheck → Lint → Tests → Security → Diff). ALL must pass.
- **Code**: remove dead code/duplicates/unused. Beginner-readable. No magic values. Immutable data patterns. Functions <50 lines. Files <400 lines. No nesting >4 levels.
- **Security**: apply `docs/security-playbook.md`. Reference rule IDs (AUTH-02, INJ-01). Pre-Commit Checklist §22. If security issue found — STOP, fix CRITICAL issues, rotate exposed secrets, review codebase for similar issues.
- **Tests**: run all. Worst-case + best-case. Adversarial: null, empty, 10k, Unicode, SQL. 80%+ coverage target.
- **Architecture**: plan first. Design for 10x/100x. Dispatch `architect` agent for system-level decisions.
- **Design**: run AI Slop Detection Checklist. Production-grade only. Research real products first. Use design skills. Invoke `design-auditor` on every UI component.
- **UX**: test interactions. Mobile-first. WCAG 2.1 AA. Skeletons > spinners. Error boundaries. Keyboard nav. Screen reader. Focus management. Touch targets 44px+. Run `ux-heuristics`.
- **Aesthetics**: typography hierarchy. Spacing rhythm. Color with purpose. Depth with restraint. "Would a Creative Director sign off?"
- **Search Audit**: did we check for existing packages/tools before writing custom code? Could we have Adopted instead of Built?
- **Diagrams**: did any architectural change affect diagrams in `docs/diagrams/`? Update them.

## Warnings
- Never `node-appwrite` in `"use client"` · Never create UI primitives manually · `lib/env.ts` not `process.env` · `redirect()` OUTSIDE try/catch · Serialise Appwrite dates · `error.tsx` must be `"use client"` · All payment keys server-only · TNG: STRING cents, `U` ≠ failed · Semantic tokens only · Use Appwrite MCP for DB exploration, SDK for app code

## Docs
- **@docs/AGENTS.md** — multi-agent orchestration, dispatch rules, handoff format, search-first protocol
- **@docs/ARCHITECTURE.md** — system design, data flow, MCP integration points, diagram index
- **@docs/CONVENTIONS.md** — naming, RSC, shadcn, payments, code quality invariants, design standards
- **@docs/security-playbook.md** — 22 sections, 100+ rules (AUTH/INJ/AUTHZ/PAY/AW), master checklists
- **@docs/SECURITY.md** — 24-rule quick-ref with playbook cross-refs
- **@docs/PAYMENTS.md** — Stripe, PayPal, TNG full API reference
- **@docs/runbooks/mcp-setup.md** — MCP server setup commands and troubleshooting
- **@docs/runbooks/** — operational procedures
- **@docs/decisions/** — ADRs

## Skills
**Design (INVOKE ON ALL UI WORK)**: frontend-design, top-design, refactoring-ui, design-auditor, web-design-guidelines, implement-design, create-design-system-rules, shadcn, microinteractions, ux-heuristics, design-everyday-things, web-typography, ios-hig-design, canvas-design · Next.js: next-best-practices, next-cache-components, next-upgrade · React: vercel-react-best-practices, vercel-composition-patterns · Security: owasp-security, vibesec-skill, sanitize, ffuf-web-fuzzing · Payments: stripe-best-practices · Deploy: deploy-to-vercel · Research: firecrawl suite · Testing: webapp-testing, agent-browser · Data: database-schema-designer · Plugins: design, brand-voice

## Compaction
Preserve: modified files list, failing tests, branch + task context, `tasks/todo.md` + `tasks/gotchas.md` contents, active Appwrite collections, current agent orchestration chain state, diagram update status
