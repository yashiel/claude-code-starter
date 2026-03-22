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
3. **Check all 8 role lenses** — is this a schema issue? Auth issue? Frontend state? Race condition? Design flaw? UX friction?
4. **Research** — use Context7/WebSearch for the specific error before guessing
5. **Fix architecturally** — not a band-aid. Would this fix survive 100x traffic?
6. **Log lesson** → `tasks/gotchas.md`

## Task Flow
1. **Research** — read relevant docs (Context7, web, MCP docs) before planning
2. **Plan** → `tasks/todo.md` with checkable items. Use `EnterPlanMode`. Think through all 8 lenses.
3. **Invoke Skills** — load all applicable skills before implementation
4. Verify plan before coding
5. Track progress as you go (TodoWrite)
6. Explain changes at each step
7. Document results in `tasks/todo.md`
8. Capture lessons → `tasks/gotchas.md`
9. **Update `MEMORY.md`** — session summary for continuity

## Publishing Protocol (Dual-Repo Strategy)
When asked to "publish to GitHub" or "push to GitHub", ALWAYS create TWO repositories:

### Repo 1: Full Project (source of truth)
- **Name**: `{project-name}` (e.g. `my-saas-app`)
- **Contains**: EVERYTHING — `CLAUDE.md`, `MEMORY.md`, `docs/`, `tasks/`, `tools/`, `.claude/`, `scripts/`, `src/`, config files, etc.
- **Purpose**: Full development context. Claude Code workspace. Planning docs, gotchas, security playbook, ADRs — the entire knowledge base.
- **Visibility**: Public or Private (user's choice)

### Repo 2: Deployable App (production source)
- **Name**: `{project-name}-app` (e.g. `my-saas-app-app`)
- **Contains**: ONLY the deployable application — the Next.js project files that cloud providers need:
  ```
  src/                  → application source code
  public/               → static assets
  package.json          → dependencies
  package-lock.json     → lockfile
  tsconfig.json         → TypeScript config
  next.config.ts        → Next.js config
  tailwind.config.ts    → Tailwind config
  postcss.config.mjs    → PostCSS config
  components.json       → shadcn config
  .env.example          → env template (NO secrets)
  .gitignore            → standard Next.js ignores
  README.md             → deployment & setup instructions only
  ```
- **Excludes**: `CLAUDE.md`, `MEMORY.md`, `docs/`, `tasks/`, `tools/`, `.claude/`, `scripts/`, `tests/` (unless CI needs them), any dev-only files
- **Purpose**: Clean, deployable source. Connect to Vercel/Appwrite Sites/Heroku/Railway for auto-deploy.
- **Visibility**: Matches Repo 1

### How to Execute
```bash
# 1. Create full project repo (already has all files)
gh repo create {project-name} --public --source=. --remote=origin --push

# 2. Create deployable repo
mkdir -p /tmp/{project-name}-app
# Copy ONLY deployable files
cp -r src/ public/ package.json package-lock.json tsconfig.json \
      next.config.ts tailwind.config.ts postcss.config.mjs \
      components.json .env.example .gitignore /tmp/{project-name}-app/
# Write a deploy-focused README
# Init git, commit, create repo, push
cd /tmp/{project-name}-app
git init && git add -A && git commit -m "Initial deployable release"
gh repo create {project-name}-app --public --source=. --remote=origin --push
```

### Keeping Repos In Sync
When the user makes changes to `src/` or config files in the full repo:
- Remind user to sync to the app repo
- Or set up a GitHub Action in the full repo that auto-copies deployable files to the app repo on push to `main`

### Why Two Repos?
- Cloud providers (Vercel, Appwrite Sites, Heroku) only need the app — no dev docs, no planning files, no security playbooks
- Full repo stays as the development workspace with all context
- App repo stays clean, minimal, fast to clone and deploy
- Separation of concerns: development context vs production artifact

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
- **Design**: run AI Slop Detection Checklist. Production-grade only. Research real products first. Use design skills. Invoke `design-auditor` on every UI component.
- **UX**: test interactions. Mobile-first. WCAG 2.1 AA. Skeletons > spinners. Error boundaries. Keyboard nav. Screen reader. Focus management. Touch targets 44px+. Run `ux-heuristics`.
- **Aesthetics**: typography hierarchy. Spacing rhythm. Color with purpose. Depth with restraint. "Would a Creative Director sign off?"

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
**Design (INVOKE ON ALL UI WORK)**: frontend-design, top-design, refactoring-ui, design-auditor, web-design-guidelines, implement-design, create-design-system-rules, shadcn, microinteractions, ux-heuristics, design-everyday-things, web-typography, ios-hig-design, canvas-design · Next.js: next-best-practices, next-cache-components, next-upgrade · React: vercel-react-best-practices, vercel-composition-patterns · Security: owasp-security, vibesec-skill, sanitize, ffuf-web-fuzzing · Payments: stripe-best-practices · Deploy: deploy-to-vercel · Research: firecrawl suite · Testing: webapp-testing, agent-browser · Data: database-schema-designer · Plugins: design, brand-voice

## Compaction
Preserve: modified files list, failing tests, branch + task context, `tasks/todo.md` + `tasks/gotchas.md` contents, active Appwrite collections
