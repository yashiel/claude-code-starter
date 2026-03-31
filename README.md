# Claude Code Starter Kit

A production-grade project template for building Next.js applications with AI-assisted development using [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). Multi-agent orchestration, 8-role thinking, search-first development, zero-AI-slop design standards, and a security playbook — ready out of the box.

Inspired by [everything-claude-code](https://github.com/affaan-m/everything-claude-code) agent patterns.

---

## What This Does

A **folder structure, configuration, and knowledge base** that makes Claude Code dramatically more effective. Instead of explaining your stack, standards, and workflows every session, you write it once — Claude reads it automatically.

**Core behaviours:**
- **8-role thinking** — System Architect, Software Engineer, DB Engineer, QA, Designer, Creative Director, UX Designer, Frontend Developer. Every task runs all 8 lenses.
- **Multi-agent orchestration** — specialised subagents dispatched automatically. See `docs/AGENTS.md`.
- **Search-first** — before writing ANY code, search existing packages/tools. Adopt > Extend > Compose > Build.
- **6-phase verification** — Build → TypeCheck → Lint → Tests → Security → Diff. All must pass.
- **Zero AI slop** — production-grade UI only. Research real products. WCAG 2.1 AA. Skeletons not spinners.
- **Memory across sessions** — `MEMORY.md` updated at session end, read at session start.

## Base Stack

Next.js 15+ (App Router, React 19) · TypeScript strict · shadcn/ui · Tailwind CSS 4 · Lucide Icons · TanStack Query (when needed) · Vercel

**Backend/auth/payments are left open** — add whatever you need (Supabase, Appwrite, Prisma, Clerk, Stripe, etc.)

---

## Quick Start

### New Project
```bash
npx create-next-app@latest my-project
cd my-project
# Copy starter files in (see "What's Inside" for structure)
```

### Existing Project
```bash
cd your-existing-project
# Copy non-conflicting files
cp path/to/starter/CLAUDE.md path/to/starter/MEMORY.md .
cp -r path/to/starter/.claude path/to/starter/tasks path/to/starter/tools .
cp -rn path/to/starter/docs .
cp path/to/starter/src/CLAUDE.md src/
cp path/to/starter/tests/CLAUDE.md tests/ 2>/dev/null
```

Then customise `CLAUDE.md`: replace `[PROJECT_NAME]`, update Stack, add your MCP servers.

### MCP Servers (optional)
```bash
claude mcp add --transport http context7 https://mcp.context7.com/mcp    # latest docs
claude mcp add --transport http figma https://mcp.figma.com/mcp          # design-to-code
claude mcp add --transport http vercel https://mcp.vercel.com            # deployments
```

---

## What's Inside

```
claude-code-starter/
├── CLAUDE.md                    ← Main brain — read every session
├── MEMORY.md                    ← Session continuity
├── .env.example                 ← Env var template
├── .claude/
│   ├── settings.json            ← Permissions, hooks
│   ├── commands/                ← /commit, /feature, /fix, /pr, /catchup
│   └── skills/                  ← Auto-invoked: plan, review, catchup
├── docs/
│   ├── AGENTS.md                ← Multi-agent orchestration patterns
│   ├── ARCHITECTURE.md          ← System design, data flow, diagrams
│   ├── CONVENTIONS.md           ← Code quality, design standards
│   ├── SECURITY.md              ← 24-rule quick reference
│   ├── security-playbook.md     ← Full security playbook (100+ rules)
│   ├── decisions/               ← ADR templates
│   ├── diagrams/                ← 7 Mermaid diagrams (ERD, class, deployment, etc.)
│   └── runbooks/mcp-setup.md    ← MCP server setup
├── src/CLAUDE.md                ← Source conventions
├── tests/CLAUDE.md              ← Testing conventions
├── tasks/
│   ├── todo.md                  ← Current plan
│   └── gotchas.md               ← Lessons learned
├── tools/                       ← scripts/, prompts/
└── scripts/CLAUDE.md            ← Script conventions
```

---

## Key Concepts

### 8-Role Thinking (every task)
| Lens | Focus |
|------|-------|
| System Architect | System fit, scale 10x/100x |
| Software Engineer | Clean code, SOLID, error handling |
| Database Engineer | Schema, indexes, migrations |
| QA Engineer | What breaks? Edge cases? |
| Designer | Production-grade, research real products |
| Creative Director | Visual identity, typography, spacing |
| UX Designer | Frictionless journey, accessibility |
| Frontend Developer | RSC, mobile-first, all states |

### Multi-Agent Orchestration
| Chain | Agents |
|-------|--------|
| Feature | planner → researcher → implementer → code-reviewer → security-reviewer |
| Bugfix | debugger → implementer → code-reviewer |
| Refactor | architect → code-reviewer → implementer |
| UI/Design | design-researcher → implementer → design-auditor → a11y-reviewer |

### Slash Commands
| Command | What It Does |
|---------|-------------|
| `/commit` | Cleanup → security check → verify → commit |
| `/feature [desc]` | 8 personas → research → plan → approve → build |
| `/fix [desc]` | Root cause → fix → regression test |
| `/pr` | Quality gate → review → PR description |
| `/catchup` | Reload context from memory + tasks + git |

### Dual-Repo Publishing
When publishing to GitHub, creates two repos:
1. **Full project** — everything (CLAUDE.md, docs, tasks, tools, src)
2. **Deployable app** — only what cloud providers need (src, configs, package.json)

---

## Customising

**Must customise**: `CLAUDE.md` (project name, stack, MCP servers) · `.env.example`

**Add your backend**: Update Stack in `CLAUDE.md` · Add env vars · Update `docs/ARCHITECTURE.md` data flow

**Add payments**: Create `docs/PAYMENTS.md` · Add provider rules to CLAUDE.md

**Don't change**: security-playbook.md · AGENTS.md · verification loop · quality gates

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Claude ignores rules | Keep CLAUDE.md critical rules near top |
| Same mistake repeated | Add to `tasks/gotchas.md`, promote to CLAUDE.md if persistent |
| High token usage | Use `/clear` + `/catchup` to reset context |
| MCP won't start | Check Node.js installed, verify env vars, see `docs/runbooks/mcp-setup.md` |
| `redirect()` silent fail | Move outside `try/catch` |

---

## Resources

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) · [MCP](https://modelcontextprotocol.io)
- [Next.js](https://nextjs.org/docs/app) · [shadcn/ui](https://ui.shadcn.com) · [Tailwind CSS](https://tailwindcss.com)
- [everything-claude-code](https://github.com/affaan-m/everything-claude-code) — agent patterns
