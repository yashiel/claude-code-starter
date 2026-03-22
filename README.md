# Claude Code Starter Kit

A production-grade project template for building Next.js applications with AI-assisted development using [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). Multi-agent orchestration, 8-role thinking, search-first development, zero-AI-slop design standards, and a 1,400-line security playbook — ready out of the box.

Inspired by [everything-claude-code](https://github.com/affaan-m/everything-claude-code) agent patterns.

---

## What This Does

This is a **folder structure, configuration, and knowledge base** that makes Claude Code dramatically more effective. Instead of explaining your stack, standards, and workflows every session, you write it once — Claude reads it automatically.

**Core behaviours this template instils:**

- **8-role thinking** — System Architect, Software Engineer, Database Engineer, QA Engineer, Designer, Creative Director, UX Designer, Frontend Developer. Every task runs through all 8 lenses before code is written.
- **Multi-agent orchestration** — specialised subagents dispatched automatically (planner, researcher, code-reviewer, security-reviewer, design-auditor, a11y-reviewer). See `docs/AGENTS.md`.
- **Search-first development** — before writing ANY code, search for existing packages, MCP tools, skills. Decision matrix: Adopt > Extend > Compose > Build.
- **6-phase verification loop** — Build → TypeCheck → Lint → Tests → Security → Diff Review. All must pass before "done".
- **Zero AI slop design** — production-grade UI only. Research real products before designing. WCAG 2.1 AA. No spinners (skeletons). Every state designed.
- **Memory across sessions** — `MEMORY.md` updated at session end, read at session start. Context survives.

---

## Stack

Next.js 15+ (App Router, React 19) · TypeScript strict · shadcn/ui · Tailwind CSS 4 · Appwrite (Auth/DB/Storage) · TanStack Query (client polling only) · Stripe + PayPal + TNG eWallet · Vercel or Appwrite Sites

---

## Quick Start

### 1. Create your Next.js project

```bash
npx create-next-app@latest my-project
# Choose: TypeScript Yes · ESLint Yes · Tailwind Yes · src/ Yes · App Router Yes
```

### 2. Copy the starter kit

```bash
cd my-project
# Clone or download this repo, then copy files in
cp -rn claude-code-starter/* .
cp -rn claude-code-starter/.claude .
cp claude-code-starter/.gitignore .
cp claude-code-starter/.env.example .
```

### 3. Set up environment

```bash
cp .env.example .env.local
# Fill in your Appwrite project ID, API key, payment keys, etc.
```

### 4. Install dependencies

```bash
npm install appwrite node-appwrite zod
npx shadcn@latest init
npx shadcn@latest add button card input   # add what you need
```

### 5. Set up MCP servers

See `docs/runbooks/mcp-setup.md` for full commands. The essentials:

```bash
# Appwrite API
claude mcp add-json appwrite-api '{
  "command": "uvx",
  "args": ["mcp-server-appwrite", "--databases"],
  "env": {
    "APPWRITE_PROJECT_ID": "your-project-id",
    "APPWRITE_API_KEY": "your-api-key",
    "APPWRITE_ENDPOINT": "https://cloud.appwrite.io/v1"
  }
}'

# Figma (design-to-code)
claude mcp add --transport http figma https://mcp.figma.com/mcp

# Context7 (latest docs for any library)
claude mcp add --transport http context7 https://mcp.context7.com/mcp

# Vercel (deployments)
claude mcp add --transport http vercel https://mcp.vercel.com
```

### 6. Customise CLAUDE.md

Open `CLAUDE.md` and replace `[PROJECT_NAME]` and `[One-line description]` with your project details.

### 7. Start

```bash
claude
```

---

## What's Inside

```
claude-code-starter/
├── CLAUDE.md                    ← Main brain — Claude reads this every session
├── MEMORY.md                    ← Session continuity — what was done, what's next
├── .env.example                 ← Environment variable template
│
├── .claude/
│   ├── settings.json            ← Permissions, hooks, tool rules
│   ├── commands/                ← /commit, /feature, /fix, /pr, /catchup
│   └── skills/                  ← Auto-invoked: plan, review, catchup
│
├── docs/
│   ├── AGENTS.md                ← Multi-agent orchestration patterns
│   ├── ARCHITECTURE.md          ← System design, data flow, MCP integration
│   ├── CONVENTIONS.md           ← Code quality, design standards, patterns
│   ├── SECURITY.md              ← 24-rule quick reference
│   ├── security-playbook.md     ← Full 1,400-line security playbook (100+ rules)
│   ├── PAYMENTS.md              ← Stripe + PayPal + TNG API reference
│   ├── decisions/               ← Architecture Decision Records
│   └── runbooks/
│       └── mcp-setup.md         ← MCP server setup guide
│
├── src/                         ← Application code
│   └── CLAUDE.md                ← Source conventions
│
├── tests/                       ← Unit, integration, e2e tests
│   └── CLAUDE.md                ← Testing conventions
│
├── tasks/
│   ├── todo.md                  ← Current plan with checkboxes
│   └── gotchas.md               ← Lessons learned (reviewed every session)
│
├── tools/
│   ├── scripts/                 ← Automation scripts
│   └── prompts/                 ← Reusable prompt templates
│
└── scripts/                     ← Build, deploy, migration scripts
    └── CLAUDE.md                ← Script conventions
```

---

## Key Concepts

### 8-Role Thinking

Every task is analysed through 8 lenses before implementation:

| Lens | What It Asks |
|------|-------------|
| System Architect | Does this fit the system? Scale to 10x/100x? |
| Software Engineer | Clean code? SOLID? Error handling? Type safety? |
| Database Engineer | Schema? Indexes? RLS? Migrations reversible? |
| QA Engineer | What breaks this? Null? 10k items? Auth bypass? |
| Designer | Production-grade? Research real products first. No AI slop. |
| Creative Director | Cohesive identity? Typography hierarchy? Awards-worthy? |
| UX Designer | Frictionless journey? Cognitive load minimised? Accessible? |
| Frontend Developer | RSC boundaries? Mobile-first? Loading/error states? |

### Multi-Agent Orchestration

Specialised agents are dispatched automatically based on task type:

| Chain | Agents |
|-------|--------|
| Feature | planner → researcher → implementer → code-reviewer → security-reviewer |
| Bugfix | debugger → implementer → code-reviewer |
| Refactor | architect → code-reviewer → implementer |
| UI/Design | design-researcher → implementer → design-auditor → a11y-reviewer |

Full details in `docs/AGENTS.md`.

### Search-First Protocol

Before writing any new code, search for existing solutions:

1. **Define need** — functionality + constraints
2. **Search** — npm, MCP tools, skills, GitHub (in parallel)
3. **Evaluate** — maintenance, community, docs, license
4. **Decide**: Adopt (use directly) > Extend (thin wrapper) > Compose (combine packages) > Build (custom)

### 6-Phase Verification Loop

Every task must pass all 6 phases before claiming "done":

| Phase | Command |
|-------|---------|
| Build | `npm run build` |
| TypeCheck | `npx tsc --noEmit` |
| Lint | `npm run lint` |
| Tests | `npm test` |
| Security | `owasp-security` / `vibesec-skill` |
| Diff Review | `git diff` — check for dead code, secrets, intent |

### Dual-Repo Publishing

When publishing to GitHub, two repos are created:

1. **Full project** (`my-app`) — everything: CLAUDE.md, docs, tasks, tools, src, scripts
2. **Deployable app** (`my-app-app`) — only what cloud providers need: src, configs, package.json

Cloud providers (Vercel, Appwrite Sites, Heroku) deploy from the `-app` repo. Development happens in the full repo.

### Memory System

- **MEMORY.md** — updated at end of every session with what was built, decisions made, and what's next
- **tasks/todo.md** — current plan with progress tracking
- **tasks/gotchas.md** — lessons learned, reviewed at every session start
- Next session starts by reading all three — context survives across sessions

---

## Slash Commands

| Command | What It Does |
|---------|-------------|
| `/commit` | Cleanup → security check → verify → commit |
| `/feature [desc]` | Review lessons → research (MCP) → plan → approve → build |
| `/fix [desc]` | Reproduce → root cause → fix → regression test |
| `/pr` | Quality gate → 8-lens review → PR description |
| `/catchup` | Reload lessons → tasks → git state → health check |

## Auto-Invoked Skills

| Skill | Triggers On |
|-------|------------|
| `plan` | "plan this", complex tasks → writes to `tasks/todo.md` |
| `review` | "review this" → 8-lens review with security rule IDs |
| `catchup` | "where were we" → full context recovery |

---

## MCP Servers

| Server | Purpose |
|--------|---------|
| Appwrite API | Manage databases, users, storage directly |
| Appwrite Docs | Latest Appwrite documentation |
| Figma | Design-to-code, screenshots, FigJam diagrams |
| Vercel | Deployments, preview URLs |
| Sentry | Error monitoring |
| Context7 | Latest docs for any library |
| Gmail | Email drafts and labels |
| Canva | Graphics and marketing visuals |

**MCP-First Rule**: When a connected tool can handle the task, use it. MCP for development workflow, SDK for application code.

---

## Documentation

| Document | Contents |
|----------|---------|
| `CLAUDE.md` | Main config — stack, rules, workflow, quality gates |
| `docs/AGENTS.md` | Agent roster, orchestration chains, dispatch rules |
| `docs/ARCHITECTURE.md` | System design, data flow, deployment strategy |
| `docs/CONVENTIONS.md` | Code quality, design standards, naming, patterns |
| `docs/SECURITY.md` | 24-rule quick reference |
| `docs/security-playbook.md` | 22 sections, 100+ rules with IDs (AUTH/INJ/AUTHZ/PAY/AW) |
| `docs/PAYMENTS.md` | Stripe, PayPal, TNG Digital full API reference |
| `docs/runbooks/mcp-setup.md` | MCP server setup commands and troubleshooting |

---

## Customising

**Do customise**: `CLAUDE.md` (project name, stack) · `docs/ARCHITECTURE.md` (collections, data flow) · `.env.example` (your vars) · `.claude/settings.json` (permissions)

**Don't change**: `security-playbook.md` · workflow orchestration rules · quality gate in `/commit` · verification loop

**MCP**: Add/remove servers based on your services — see `docs/runbooks/mcp-setup.md`

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Claude ignores rules | CLAUDE.md may be too long. Keep critical rules near the top. |
| Same mistake repeated | Add to `tasks/gotchas.md`. If persistent, promote to CLAUDE.md. |
| High token usage | Reduce Appwrite MCP flags. Use `/clear` + `/catchup`. |
| MCP won't start | Check `uvx --version`. Verify env vars. See `docs/runbooks/mcp-setup.md`. |
| `node-appwrite` in client | Use `appwrite` (browser SDK) in `"use client"` files. |
| `redirect()` silent fail | Move outside `try/catch`. |
| Figma MCP broken | `claude mcp remove figma && claude mcp add --transport http figma https://mcp.figma.com/mcp` |

---

## Resources

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) · [MCP Docs](https://modelcontextprotocol.io)
- [Appwrite](https://appwrite.io/docs) · [Appwrite MCP](https://appwrite.io/docs/tooling/mcp/claude-code)
- [Figma MCP](https://mcp.figma.com) · [Next.js](https://nextjs.org/docs/app) · [shadcn/ui](https://ui.shadcn.com)
- [Stripe](https://docs.stripe.com) · [PayPal](https://developer.paypal.com) · [TNG](https://miniprogram.tngdigital.com.my/docs)
- [everything-claude-code](https://github.com/affaan-m/everything-claude-code) — agent patterns inspiration
