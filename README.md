# Claude Code Starter Kit

A production-grade monorepo template for building web, mobile, and static applications with AI-assisted development using [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). Built on **Karpathy Principles**, the **MiHCM Design System**, multi-agent orchestration, and 8-role thinking.

---

## Philosophy

**Karpathy Principles** govern every line of code in this template:

1. **Think Before Coding** — state assumptions, surface tradeoffs, ask when unclear
2. **Simplicity First** — minimum code that solves the problem, nothing speculative
3. **Surgical Changes** — touch only what you must, match existing style
4. **Goal-Driven Execution** — define verifiable success criteria, loop until verified

These four principles are the highest priority. Everything else serves them.

---

## Stack

| Layer | Technology | Version |
|-------|-----------|---------|
| Framework | Next.js (App Router) | 16.x |
| Runtime | React | 19.x |
| Language | TypeScript (strict mode) | 6.x |
| Validation | Zod | 4.x |
| Client State | Zustand | 5.x |
| Data Fetching | TanStack Query | 5.x |
| Styling | Tailwind CSS (semantic tokens only) | 4.x |
| Design System | [MiHCM Design System](https://designsystem.mihcm.com/) | latest |
| Icons | MiHCM Icons (1,703 Lucide-based) | latest |
| Mobile | Expo (React Native, NativeWind, Expo Router) | latest |
| Deployment | Vercel | — |

### Enforced Libraries

| Domain | Library | Version |
|--------|---------|---------|
| Forms | `@tanstack/react-form` + `zod` | 1.x / 4.x |
| Tables | `@tanstack/react-table` | 8.x |
| Virtualization | `@tanstack/react-virtual` | 3.x |
| i18n | `next-intl` | 4.x |
| Dates | `date-fns` + `date-fns-tz` | 4.x / 3.x |
| Data Viz | `d3` | 7.x |
| Drag & Drop | `@dnd-kit/core` | 6.x |
| Command Palette | `cmdk` | 1.x |
| Rich Text | MiHCM `RichTextEditor` (Lexical) | 0.x |
| Animation | `motion` | 12.x |
| File Upload | `react-dropzone` | 15.x |
| Error Monitoring | `@sentry/nextjs` | 10.x |
| Analytics | `posthog-js` | 1.x |
| Unit/Integration Tests | `vitest` + `@testing-library/react` | 4.x / 16.x |
| E2E Tests | `playwright` | 1.x |
| Emails | `@react-email` | 4.x |

**No other UI libraries.** No shadcn/ui, Radix (direct), Material UI, Chakra, Ant Design, or Mantine. All components come from `@yashiel/mihcm-ui`. No hardcoded CSS values — semantic tokens only. Never build custom solutions when an enforced library covers the need.

**Backend, auth, and payments are left open** — add whatever fits your needs.

---

## Quick Start

### Prerequisites
- Node.js 18+
- GitHub PAT with `read:packages` scope (for `@yashiel` registry)
- Claude Code CLI

### Setup

```bash
# 1. Clone or copy the template
git clone <this-repo> my-project && cd my-project

# 2. Configure npm registry for MiHCM packages
echo '//npm.pkg.github.com/:_authToken=YOUR_TOKEN' >> ~/.npmrc
echo '@yashiel:registry=https://npm.pkg.github.com' >> ~/.npmrc

# 3. Install MiHCM packages
pnpm add @yashiel/mihcm-ui @yashiel/mihcm-theme @yashiel/mihcm-tokens @yashiel/mihcm-icons

# 4. Register MCP servers
claude mcp add --transport http --scope project mihcm https://designsystem.mihcm.com/mcp
claude mcp add --transport http context7 https://mcp.context7.com/mcp
claude mcp add --transport http figma https://mcp.figma.com/mcp
claude mcp add --transport http vercel https://mcp.vercel.com

# 5. Customise CLAUDE.md
# Replace [PROJECT_NAME] and [One-line description]
```

### Existing Project

```bash
cd your-existing-project

# Copy core files
cp path/to/starter/CLAUDE.md path/to/starter/MEMORY.md .
cp -r path/to/starter/.claude path/to/starter/tasks path/to/starter/tools .
cp -rn path/to/starter/docs .
cp path/to/starter/.mcp.json .
```

---

## Project Structure

```
claude-code-starter/
├── CLAUDE.md                        <- Main brain — Karpathy principles, stack, workflow
├── MEMORY.md                        <- Session continuity across conversations
├── .mcp.json                        <- MiHCM MCP server registration
├── .env.example                     <- Environment variable template
├── .claude/
│   ├── settings.json                <- Permissions, hooks, MiHCM tool allowlist
│   ├── commands/                    <- /commit, /feature, /fix, /pr, /catchup
│   └── skills/                      <- Auto-invoked: plan, review, catchup
├── src/
│   ├── web/                         <- MiHCM Next.js project
│   │   ├── app/                     <- App Router (layout, page, loading, error)
│   │   │   ├── (auth)/              <- Login, register routes
│   │   │   ├── (dashboard)/         <- Protected routes
│   │   │   └── api/                 <- Route handlers
│   │   ├── components/
│   │   │   ├── ds/                  <- MiHCM CLI-copied components
│   │   │   └── features/            <- Domain composites from MiHCM
│   │   ├── lib/                     <- env.ts, errors.ts, utils.ts
│   │   ├── actions/                 <- Server Actions by domain
│   │   ├── hooks/                   <- Custom React hooks
│   │   ├── stores/                  <- Zustand stores by domain
│   │   ├── providers/               <- QueryClientProvider, ThemeProvider
│   │   └── types/                   <- Shared TypeScript types
│   ├── mobile/                      <- Expo (React Native) project
│   │   ├── app/                     <- Expo Router screens
│   │   ├── components/              <- MiHCM native components
│   │   ├── hooks/                   <- Mobile-specific hooks
│   │   ├── stores/                  <- Zustand stores
│   │   └── types/                   <- Shared types
│   └── static/                      <- Plain HTML/CSS/JS project
│       ├── pages/                   <- HTML pages
│       ├── css/                     <- Stylesheets (MiHCM tokens via CSS vars)
│       ├── js/                      <- Vanilla JavaScript
│       └── assets/                  <- Images, fonts, icons
├── docs/
│   ├── AGENTS.md                    <- Multi-agent orchestration patterns
│   ├── ARCHITECTURE.md              <- System design, data flow, monorepo layout
│   ├── CONVENTIONS.md               <- Code quality, MiHCM usage, design standards
│   ├── SECURITY.md                  <- 24-rule quick reference
│   ├── security-playbook.md         <- Full security playbook (100+ rules)
│   ├── decisions/                   <- Architecture Decision Records
│   ├── diagrams/                    <- 7 Mermaid diagrams
│   └── runbooks/mcp-setup.md       <- MCP server setup guide
├── tasks/
│   ├── todo.md                      <- Current work plan
│   └── gotchas.md                   <- Lessons learned
└── tools/                           <- Scripts and prompt templates
```

---

## MCP Servers

The template connects Claude Code to external services via MCP:

| Server | Purpose | Required |
|--------|---------|----------|
| **MiHCM** | Component search, token lookup, code review, RSC boundary checks | Yes (all UI work) |
| **Context7** | Up-to-date docs for any library | Recommended |
| **Figma** | Design-to-code context, screenshots | Optional |
| **Vercel** | Deployments, preview URLs | Optional |

MiHCM MCP auto-loads for any UI/design task. See [docs/runbooks/mcp-setup.md](docs/runbooks/mcp-setup.md) for setup details.

---

## How It Works

### 8-Role Thinking

Every task runs through all 8 lenses before a single line of code is written:

| Lens | Focus |
|------|-------|
| System Architect | System fit, data flow, scale 10x/100x |
| Software Engineer | Clean code, SOLID, types, error handling |
| Database Engineer | Schema, indexes, reversible migrations |
| QA Engineer | What breaks? Null, 10k items, Unicode, auth bypass |
| Designer | MiHCM components, no hardcoded values, production-grade |
| Creative Director | Typography hierarchy, color via MiHCM tokens, spacing rhythm |
| UX Designer | Frictionless journey, cognitive load, progressive disclosure |
| Frontend Developer | RSC boundaries, accessible, mobile-first, all states |

### Multi-Agent Orchestration

Specialised agents dispatched automatically based on task type:

| Chain | Agents |
|-------|--------|
| Feature | planner -> researcher -> MiHCM MCP -> implementer -> code-reviewer -> security-reviewer |
| Bug Fix | planner -> TDD guide -> code-reviewer |
| Refactor | architect -> code-reviewer -> refactor cleaner |
| UI Component | design researcher + MiHCM MCP -> planner -> implementer -> design auditor -> a11y reviewer |

### Verification Loop

Six phases that must all pass before any task is considered done:

```
Build -> TypeCheck -> Lint -> Tests -> Security -> Diff Review
```

### Slash Commands

| Command | What It Does |
|---------|-------------|
| `/commit` | Cleanup, security check, verify, commit |
| `/feature [desc]` | 8 personas, research, plan, approve, build |
| `/fix [desc]` | Root cause analysis, fix, regression test |
| `/pr` | Quality gate, review, create PR |
| `/catchup` | Reload context from memory + tasks + git |

---

## MiHCM Design System

All UI work uses the [MiHCM Design System](https://designsystem.mihcm.com/):

- **60+ components** — Button, DataTable, Card, Dialog, Sidebar, Form, Chart, and more
- **Design tokens** — colors (primary navy, accent orange), spacing (4px base), typography, motion
- **1,703 icons** — Lucide-based, via `@yashiel/mihcm-icons`
- **MCP server** — 20+ tools for component search, token lookup, code review
- **CLI** — `npx @yashiel/mihcm-cli add <Component>` for copy-paste mode
- **AI UI** — Zod-validated generative UI via `@yashiel/mihcm-ai-ui`

### Import Pattern

```tsx
// Subpath imports for tree-shaking
import { Button } from '@yashiel/mihcm-ui/Button';
import { DataTable } from '@yashiel/mihcm-ui/DataTable';
```

---

## Customising

| What | Where |
|------|-------|
| Project name and description | `CLAUDE.md` top section |
| Backend/auth/payments | Update Stack in `CLAUDE.md`, add env vars, update `docs/ARCHITECTURE.md` |
| MCP servers | `.mcp.json` and `docs/runbooks/mcp-setup.md` |
| Environment variables | `.env.example` |
| Permissions and hooks | `.claude/settings.json` |

**Don't change**: Karpathy Principles section, security-playbook.md, verification loop, quality gates.

---

## Dual-Repo Publishing

When publishing to GitHub, the template creates two repositories:

1. **`{project-name}`** — everything (CLAUDE.md, docs, tasks, tools, src, configs)
2. **`{project-name}-app`** — deployable only (src, configs, package.json)

Cloud providers only need the app. Dev context stays separate.

---

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Claude ignores rules | Karpathy Principles and stack enforcement are at the top of CLAUDE.md |
| Same mistake repeated | Add to `tasks/gotchas.md`, promote to CLAUDE.md if persistent |
| High token usage | Use `/clear` + `/catchup` to reset context |
| MCP not connecting | Check `claude mcp list`, restart session, see `docs/runbooks/mcp-setup.md` |
| MiHCM packages not found | Verify `~/.npmrc` has `@yashiel:registry=https://npm.pkg.github.com` |

---

## Resources

- [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview) | [MCP](https://modelcontextprotocol.io)
- [MiHCM Design System](https://designsystem.mihcm.com/) | [Tailwind CSS 4](https://tailwindcss.com)
- [Next.js App Router](https://nextjs.org/docs/app) | [Zustand](https://zustand.docs.pmnd.rs/) | [TanStack Query](https://tanstack.com/query)
- [Zod](https://zod.dev) | [Expo](https://docs.expo.dev)
