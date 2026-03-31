# [PROJECT_NAME]

> [One-line description]

## Stack
Next.js 15+ (App Router, React 19) · TypeScript strict · shadcn/ui · Tailwind CSS 4 · Lucide Icons · TanStack Query (client polling only) · Vercel

## MCP Servers (connected tools)
- **Figma** — `get_design_context` for design-to-code, `get_screenshot`, `generate_diagram` (FigJam)
- **Vercel** — deployments, toolbar threads
- **Sentry** — error monitoring and debugging
- **Context7** — up-to-date docs for any library (Next.js, React, etc.)

Setup guide: `docs/runbooks/mcp-setup.md`

**MCP-First Rule**: When a connected MCP tool can handle the task, use it. Figma MCP for design context > guessing. Context7 for latest docs > training data.

## Thinking Mindset
On EVERY task, think through ALL eight lenses before writing a single line of code:

### Engineering Lenses
| Role | What You Ask |
|------|-------------|
| **System Architect** | Does this fit the system? Data flow? Boundaries? Scale to 10x/100x? |
| **Software Engineer** | Clean code? SOLID? Error handling? Edge cases? Type safety? |
| **Database Engineer** | Schema correct? Indexes? Queries efficient? Migrations reversible? |
| **QA Engineer** | What breaks this? Null? Empty? 10k items? Unicode? Concurrent users? Auth bypass? |

### Design Lenses (MANDATORY for any UI/UX work)
| Role | What You Ask |
|------|-------------|
| **Designer** | Production-grade? No AI slop — every pixel intentional. Research real products (Dribbble, Awwwards, Apple HIG, Material 3) before designing. |
| **Creative Director** | Cohesive visual identity? Typography hierarchy? Color story? Spacing rhythm? Crafted or generated? |
| **UX Designer** | Frictionless journey? Cognitive load minimized? Feedback for every action? Error recovery graceful? Progressive disclosure? |
| **Frontend Developer** | RSC boundaries? Accessible? Mobile-first? Loading/error states? Performance? Touch targets 44px+? |

This is not optional. Every feature, every fix, every refactor — run all eight lenses.

## Design Philosophy (ZERO AI SLOP)
Every UI element must pass these gates. If it looks like AI generated it, it fails.

### Non-Negotiable Standards
1. **Accessibility is #1** — WCAG 2.1 AA minimum. Keyboard navigable. Screen reader tested. Color contrast 4.5:1+. Focus indicators visible. `aria-*` correct. Skip links. Reduced motion support.
2. **Aesthetics are #1** — Typography with purpose (max 2 typefaces, clear hierarchy). Intentional whitespace. Consistent spacing scale (4px/8px base). Color palette with meaning. Depth through subtle shadows.
3. **Design Philosophies are #1** — Gestalt (proximity, similarity, closure), Fitts's Law, Hick's Law, Miller's Law (7±2 chunks), Jakob's Law.

### Before Building ANY UI
1. **Research** — 3-5 real production examples (WebSearch/Firecrawl). Study Apple, Linear, Vercel, Stripe, Notion.
2. **Invoke design skills** — `frontend-design`, `refactoring-ui`, `top-design`, `web-design-guidelines`, `design-auditor`, `shadcn`. Always.
3. **Plan the design system** — spacing scale, color tokens, typography scale, component inventory.
4. **Mobile-first** — 375px first, then scale up.
5. **Interaction design** — every interactive element: default, hover, focus, active, disabled, loading states.

### AI Slop Detection Checklist (run on every UI change)
- [ ] Real product or demo/tutorial?
- [ ] Would a design-conscious user trust this with their credit card?
- [ ] Clear visual hierarchy (one primary action per view)?
- [ ] Transitions/animations purposeful or decorative?
- [ ] Real copy (not "Lorem ipsum" or "Click here")?
- [ ] Every icon has text label or aria-label?
- [ ] Empty state designed (not just "No data")?
- [ ] Error messages helpful and human?
- [ ] Loading state is skeleton, not spinner?
- [ ] Works without JavaScript?
- [ ] Would Dieter Rams approve?

## Principles
- **Simplicity First** — minimal code, clean solutions
- **No Lazy Fixes** — root cause, not symptoms. Senior standards.
- **Minimal Impact** — touch only what's necessary
- **Systems > Prompts** — build reusable skills/hooks/utils
- **Verification > Generation** — proving > writing
- **Iteration > Perfection** — ship, learn, update `tasks/gotchas.md`
- **Plan Before Code** — ALWAYS enter planning mode first. No exceptions.
- **Skills Are Mandatory** — if a skill exists for the task, USE IT.
- **Search First** — Adopt > Extend > Compose > Build. Never reinvent what exists.
- **Research First** — Context7/WebSearch/Firecrawl for current info. Never rely on stale training data.
- **Immutability** — new objects, never mutate. Pure functions over side effects.
- **Hooks Over Prompts** — deterministic enforcement via hooks > probabilistic instructions.
- **Memory Is Continuous** — update `MEMORY.md` at session end. Read at session start.
- **Observations Evolve** — mistakes → gotchas → rules → skills. Confidence: 0.3 → 0.6 → 0.9.

## Workflow
1. **Plan Mode ALWAYS** — plan ANY 2+ step task. STOP and re-plan if sideways. All 8 lenses.
2. **Search First** — existing packages/MCP tools/skills/patterns. Adopt > Extend > Compose > Build.
3. **Research Before Building** — Context7/WebSearch/Firecrawl for latest docs/APIs/patterns.
4. **Skills First** — check available skills. 1% chance = load it. Design skills ALWAYS for UI.
5. **Agent Orchestration** — dispatch subagents per `docs/AGENTS.md`:
   - **Feature**: planner → researcher → implementer → code-reviewer → security-reviewer
   - **Bugfix**: debugger → implementer → code-reviewer
   - **Refactor**: architect → code-reviewer → implementer
   - **UI/Design**: design researcher → implementer → design-auditor → a11y-reviewer
6. **Verify Before Done** — 6-phase: Build → TypeCheck → Lint → Tests → Security → Diff.
7. **Self-Improvement** — gotcha (0.3) → pattern (0.6) → rule (0.9).
8. **Demand Elegance** — non-trivial: "is there a cleaner way?" Simple: don't over-engineer.
9. **Autonomous Bugs** — just fix it. Root cause, not symptoms.
10. **Memory Discipline** — update at session end. Read at session start.

## Verification Loop (run before ANY completion claim)

| Phase | Command | Fail Action |
|-------|---------|-------------|
| 1. Build | `npm run build` | Fix, re-run |
| 2. TypeCheck | `npx tsc --noEmit` | Fix, re-run |
| 3. Lint | `npm run lint` | Fix, re-run |
| 4. Tests | `npm test` | Fix, re-run |
| 5. Security | Run `owasp-security` / `vibesec-skill` | Fix CRITICAL immediately |
| 6. Diff Review | `git diff` — review all changes | Remove dead code, check secrets |

If ANY phase fails, task is NOT done. Fix and re-verify.

## Persona Protocol (runs on EVERY task)

Before writing ANY code: state task → run all 8 personas → identify conflicts → resolve → proceed.

### Persona Checklist
- [ ] **System Architect** — fits system? data flow? boundaries? scale 10x/100x?
- [ ] **Software Engineer** — clean? SOLID? error handling? edge cases? types?
- [ ] **Database Engineer** — schema? indexes? queries? migrations reversible?
- [ ] **QA Engineer** — what breaks? null? empty? 10k? unicode? concurrent? auth bypass?
- [ ] **Designer** — production-grade? researched 3-5 real examples? no AI slop?
- [ ] **Creative Director** — visual identity? typography? color story? spacing rhythm?
- [ ] **UX Designer** — frictionless? cognitive load? feedback? error recovery? progressive disclosure?
- [ ] **Frontend Developer** — RSC? accessible? mobile-first? all states? performance?

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

| Task | Skills |
|------|--------|
| ANY UI | `frontend-design`, `refactoring-ui`, `shadcn`, `ux-heuristics`, `microinteractions`, `web-design-guidelines`, `design-auditor` |
| Page/layout | Above + `top-design`, `web-typography`, `design-everyday-things` |
| Planning | `plan`, `brainstorm` |
| Bug fix | `systematic-debugging`, `fix` |
| Code review | `review`, `clean-code` |
| Auth/security | `owasp-security`, `vibesec-skill` |
| Database | `database-schema-designer` + `docs/security-playbook.md` §10 |
| React | `vercel-react-best-practices`, `vercel-composition-patterns` |
| Next.js | `next-best-practices`, `next-cache-components` |
| shadcn | `shadcn` (run `docs` first) |
| Figma | `implement-design`, `create-design-system-rules` |
| Deploy | `deploy-to-vercel` |
| Tests | `webapp-testing`, `agent-browser` |
| Research | `firecrawl-search`, `firecrawl-scrape`, `firecrawl-crawl` |
| Content | `brand-voice` |

**Rules**: 1% chance = load it. Multiple apply = load ALL. Design skills ALWAYS for UI.

## Memory System

**Session start**: read `MEMORY.md` → `tasks/todo.md` → `tasks/gotchas.md` → announce context.
**Session end**: update all three → commit.
**During**: update on completion, mistakes, decisions.

## Diagrams (MANDATORY — `docs/diagrams/`)

Mermaid format. Update when architecture changes.

| Diagram | File | When to Update |
|---------|------|---------------|
| ERD | `erd.md` | Any table/relationship change |
| Class | `class.md` | New service, model, or major refactor |
| Deployment | `deployment.md` | Infrastructure or provider change |
| Use Case | `use-cases.md` | New user-facing feature |
| Sequence | `sequences.md` | New API/auth flow |
| Activity | `activities.md` | New business process |
| State Machine | `state-machines.md` | New stateful entity |

**Rules**: Create when first built. Update in SAME commit. ERD + Sequence = highest priority.

## Error Handling Protocol
1. Read full error, understand root cause
2. Use `systematic-debugging` skill
3. Check all 8 lenses
4. Research via Context7/WebSearch before guessing
5. Fix architecturally — survives 100x traffic?
6. Log lesson → `tasks/gotchas.md`

## Task Flow
Run Personas → Auto-Load Skills → Research → Plan (`tasks/todo.md`) → Verify plan → Build → Track → Verify (6-phase) → Lessons → Memory

## Publishing Protocol (Dual-Repo Strategy)
When publishing to GitHub, ALWAYS create TWO repositories:

1. **`{project-name}`** — EVERYTHING (CLAUDE.md, docs/, tasks/, tools/, .claude/, src/, configs). Development workspace.
2. **`{project-name}-app`** — Deployable only: `src/ public/ package.json package-lock.json tsconfig.json next.config.ts tailwind.config.ts postcss.config.mjs components.json .env.example .gitignore README.md`. Excludes all dev docs/tools.

**Execute**: `gh repo create {name} --public --source=. --push` for repo 1. Copy deployable files to `/tmp/{name}-app`, init git, create repo 2.
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
  api/             → route handlers
src/components/ui/ → shadcn (CLI-managed, never create manually)
src/components/features/ → domain composites from ui/
src/lib/           → env.ts (zod), errors.ts, utils.ts (cn), [backend client]
src/actions/       → Server Actions by domain
src/hooks/         → use-[name].ts
src/providers/     → QueryClientProvider, ThemeProvider
src/types/         → shared types
tasks/             → todo.md, gotchas.md
tools/             → scripts/, prompts/
docs/              → ARCHITECTURE, CONVENTIONS, SECURITY, security-playbook, decisions/, runbooks/, diagrams/
```

## Data Fetching
Server Components → reads · Server Actions → writes (zod validation, `revalidatePath`) · Route Handlers → webhooks/external APIs · TanStack Query → client polling/optimistic only · `Promise.all` for parallel · `<Suspense>` to stream · `React.cache()` for dedup

## Rules

**shadcn**: CLI only (`npx shadcn@latest add`). Semantic tokens only (`bg-primary` not `bg-blue-500`). `gap-*` not `space-*`. `size-*` for equal dims. Run `docs` before using.

**React**: No async client components. Serialisable props only across RSC boundary. Composition > booleans. `handleVerbNoun`. Avoid barrel files.

## Quality Gate (every change)
- **Verification**: 6-phase loop. ALL must pass.
- **Code**: no dead code/duplicates. Functions <50 lines. Files <400 lines. No nesting >4 levels. Immutable.
- **Security**: apply `docs/security-playbook.md`. Reference rule IDs. Pre-Commit Checklist §22.
- **Tests**: worst-case + best-case. Adversarial: null, empty, 10k, Unicode, SQL. 80%+ coverage.
- **Architecture**: plan first. Design for 10x/100x.
- **Design**: AI Slop Detection Checklist. Production-grade only. Research first. `design-auditor` on every UI.
- **UX**: mobile-first. WCAG 2.1 AA. Skeletons > spinners. Keyboard nav. Touch targets 44px+.
- **Aesthetics**: typography hierarchy. Spacing rhythm. Color with purpose. "Would a Creative Director sign off?"
- **Search Audit**: did we check existing packages/tools before writing custom code?
- **Diagrams**: did architecture change? Update `docs/diagrams/`.

## Warnings
- Never create UI primitives manually · `lib/env.ts` not `process.env` · `redirect()` OUTSIDE try/catch · `error.tsx` must be `"use client"` · Semantic tokens only

## Docs
- **@docs/AGENTS.md** — multi-agent orchestration, dispatch rules, search-first protocol
- **@docs/ARCHITECTURE.md** — system design, data flow, diagram index
- **@docs/CONVENTIONS.md** — naming, RSC, shadcn, code quality, design standards
- **@docs/security-playbook.md** — 22 sections, 100+ rules (AUTH/INJ/AUTHZ), master checklists
- **@docs/SECURITY.md** — 24-rule quick-ref with playbook cross-refs
- **@docs/runbooks/** — operational procedures, MCP setup
- **@docs/decisions/** — ADRs

## Skills
**Design (ALL UI WORK)**: frontend-design, top-design, refactoring-ui, design-auditor, web-design-guidelines, implement-design, create-design-system-rules, shadcn, microinteractions, ux-heuristics, design-everyday-things, web-typography, ios-hig-design, canvas-design · Next.js: next-best-practices, next-cache-components, next-upgrade · React: vercel-react-best-practices, vercel-composition-patterns · Security: owasp-security, vibesec-skill, sanitize · Deploy: deploy-to-vercel · Research: firecrawl suite · Testing: webapp-testing, agent-browser · Data: database-schema-designer · Plugins: design, brand-voice

## Compaction
Preserve: modified files list, failing tests, branch + task context, `tasks/todo.md` + `tasks/gotchas.md` contents, current agent orchestration chain state, diagram update status
