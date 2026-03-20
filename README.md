# Claude Code Starter Kit — Complete Guide

A ready-to-use project template for building Next.js applications with AI-assisted development using [Claude Code](https://docs.anthropic.com/en/docs/claude-code/overview). This guide explains everything from scratch — no prior experience with Claude Code required.

---

## Table of Contents

1. [What Is This?](#1-what-is-this)
2. [What's Inside](#2-whats-inside)
3. [Prerequisites](#3-prerequisites)
4. [Step-by-Step Setup](#4-step-by-step-setup)
5. [MCP Server Setup](#5-mcp-server-setup)
6. [Every File and Folder Explained](#6-every-file-and-folder-explained)
7. [How to Start a New Project](#7-how-to-start-a-new-project)
8. [How Claude Code Uses These Files](#8-how-claude-code-uses-these-files)
9. [Daily Workflow](#9-daily-workflow)
10. [Slash Commands Reference](#10-slash-commands-reference)
11. [Auto-Invoked Skills Reference](#11-auto-invoked-skills-reference)
12. [Adding Your Requirements and Documents](#12-adding-your-requirements-and-documents)
13. [Customising for Your Project](#13-customising-for-your-project)
14. [Maintaining Over Time](#14-maintaining-over-time)
15. [FAQ and Troubleshooting](#15-faq-and-troubleshooting)

---

## 1. What Is This?

This is a **folder structure and configuration template** that makes Claude Code dramatically more effective at building your Next.js applications.

Think of it like onboarding a new developer to your team. Instead of explaining your tech stack, coding standards, security rules, and project structure every single time you start a conversation, you write it down once — and Claude reads it automatically at the start of every session.

This template is pre-configured for:
- **Next.js 15+** with App Router and React 19
- **shadcn/ui** for components
- **Tailwind CSS 4** for styling
- **Appwrite** for backend (authentication, database, file storage)
- **TanStack Query** for client-side data needs
- **Stripe + PayPal + TNG Digital eWallet** for payments
- **Vercel or Appwrite Sites** for deployment
- **MCP servers** for Appwrite, Figma, Vercel, Sentry, Context7, Gmail, Canva, and more

---

## 2. What's Inside

```
claude-code-starter/
│
├── CLAUDE.md                        ← THE MOST IMPORTANT FILE
├── README.md                        ← This guide
├── .env.example                     ← Environment variable template
├── .gitignore                       ← Files Git should ignore
│
├── .claude/                         ← Claude Code configuration
│   ├── settings.json                ← Permissions, hooks, tool rules
│   ├── commands/                    ← Custom slash commands (/commit, /feature, /fix, /pr, /catchup)
│   └── skills/                      ← Auto-invoked AI workflows (plan, review, catchup)
│
├── docs/                            ← Project documentation
│   ├── ARCHITECTURE.md              ← System design, data flow, MCP integration points
│   ├── CONVENTIONS.md               ← Coding standards and patterns
│   ├── SECURITY.md                  ← 24-rule security quick-reference
│   ├── security-playbook.md         ← Full 1,400-line security playbook
│   ├── PAYMENTS.md                  ← Stripe + PayPal + TNG API reference
│   ├── decisions/                   ← Architecture Decision Records
│   └── runbooks/                    ← Operational procedures
│       └── mcp-setup.md             ← MCP server setup guide with commands
│
├── src/                             ← Your application code
│   └── CLAUDE.md                    ← Source conventions + code templates
│
├── tests/                           ← Test files (unit/, integration/, e2e/)
│   └── CLAUDE.md                    ← Testing conventions
│
├── tasks/                           ← Task tracking
│   ├── todo.md                      ← Current task plan
│   └── gotchas.md                   ← Lessons learned
│
├── tools/                           ← Automation
│   ├── scripts/                     ← Shell/Node scripts
│   └── prompts/                     ← Saved prompt templates
│
└── scripts/                         ← Build, deploy, migration scripts
    └── CLAUDE.md                    ← Script conventions
```

---

## 3. Prerequisites

1. **Claude Code** — [Installation guide](https://docs.anthropic.com/en/docs/claude-code/overview)
2. **Node.js 18+** — [nodejs.org](https://nodejs.org)
3. **uv** (for Appwrite MCP) — `curl -LsSf https://astral.sh/uv/install.sh | sh`
4. **An Appwrite account** — [cloud.appwrite.io](https://cloud.appwrite.io)
5. **A Figma account** — [figma.com](https://figma.com) (for design-to-code workflows)
6. **Git** — for version control

Optional:
- **Stripe account** — [dashboard.stripe.com](https://dashboard.stripe.com)
- **PayPal developer account** — [developer.paypal.com](https://developer.paypal.com)
- **TNG Digital developer account** — [miniprogram.tngdigital.com.my](https://miniprogram.tngdigital.com.my)

---

## 4. Step-by-Step Setup

### Step 1: Create your Next.js project

```bash
npx create-next-app@latest my-project
```

Choose: TypeScript **Yes** · ESLint **Yes** · Tailwind CSS **Yes** · `src/` directory **Yes** · App Router **Yes**

### Step 2: Copy the starter kit into your project

```bash
cd my-project
unzip claude-code-starter.zip
cp -rn claude-code-starter/* .
cp -rn claude-code-starter/.* . 2>/dev/null
```

### Step 3: Set up environment variables

```bash
cp .env.example .env.local
# Open .env.local and fill in your Appwrite project ID, API key, etc.
```

### Step 4: Install dependencies

```bash
npm install appwrite node-appwrite zod
npm install @tanstack/react-query                              # if needed
npm install stripe @stripe/react-stripe-js @stripe/stripe-js   # if using Stripe
npm install @paypal/react-paypal-js                            # if using PayPal
```

### Step 5: Initialise shadcn/ui

```bash
npx shadcn@latest init
npx shadcn@latest add button card input                        # add components you need
```

### Step 6: Customise CLAUDE.md

Open `CLAUDE.md` and replace `[PROJECT_NAME]` and `[One-line description]` with your project details.

### Step 7: Set up MCP servers (see next section)

### Step 8: Start Claude Code

```bash
claude
```

---

## 5. MCP Server Setup

MCP (Model Context Protocol) servers connect Claude Code to external services like Appwrite, Figma, and Vercel. This means Claude can directly manage your database, read your Figma designs, deploy to Vercel, and more — without you copying and pasting between tools.

The full setup guide with all commands is at `docs/runbooks/mcp-setup.md`. Here's the essential setup:

### What is MCP?

Imagine you're working with a developer who can only communicate via text chat. Normally, if you wanted them to create a database table, you'd have to:
1. Log into Appwrite Console yourself
2. Create the collection
3. Come back and tell the developer what you did

With MCP, Claude Code connects directly to Appwrite. You say "create a users collection with name, email, and role fields" and Claude does it — right there in the terminal.

### Required: Appwrite MCP

This is the most important MCP server. It lets Claude manage your Appwrite project directly.

```bash
# Appwrite API — manage databases, users, storage, functions
claude mcp add-json appwrite-api '{
  "command": "uvx",
  "args": ["mcp-server-appwrite", "--users", "--databases", "--storage", "--functions", "--teams"],
  "env": {
    "APPWRITE_PROJECT_ID": "your-project-id",
    "APPWRITE_API_KEY": "your-api-key",
    "APPWRITE_ENDPOINT": "https://cloud.appwrite.io/v1"
  }
}'

# Appwrite Docs — access latest documentation
claude mcp add-json appwrite-docs '{
  "command": "npx",
  "args": ["mcp-remote", "https://mcp-for-docs.appwrite.io"]
}'
```

**Replace** `your-project-id` and `your-api-key` with your actual values from the Appwrite Console.

**Token tip**: Each `--flag` adds tools to Claude's context window and uses tokens. Start with just `--databases` and add more as needed:
- `--databases` — collections, documents, attributes, indexes (most common)
- `--users` — user management
- `--storage` — file upload/download
- `--functions` — serverless functions
- `--teams` — team management

### Required: Figma MCP

For design-to-code workflows — Claude reads your Figma designs and generates matching code.

If you're using **claude.ai** (web), Figma is already available as a cloud MCP server — just connect it in Settings.

For **Claude Code CLI**:
```bash
claude mcp add --transport http figma https://mcp.figma.com/mcp
```

**Key Figma MCP tools**:
- `get_design_context` — the primary tool. Give it a Figma node and get design specs for code generation.
- `get_screenshot` — capture a screenshot of any Figma node
- `generate_diagram` — create flowcharts, sequence diagrams, gantt charts in FigJam
- `get_code_connect_map` / `add_code_connect_map` — link Figma components to code components

### Recommended: Other MCP Servers

```bash
# Vercel — deployments and toolbar
claude mcp add --transport http vercel https://mcp.vercel.com

# Sentry — error monitoring
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp

# Context7 — latest docs for ANY library
claude mcp add --transport http context7 https://mcp.context7.com/mcp

# Gmail — email drafts
claude mcp add --transport http gmail https://gmail.mcp.claude.com/mcp

# Canva — graphics
claude mcp add --transport http canva https://mcp.canva.com/mcp
```

### Verify your setup

```bash
claude mcp list
```

You should see all your configured servers listed. If any show errors, check `docs/runbooks/mcp-setup.md` for troubleshooting.

### When to use MCP vs SDK

This is important to understand:

| Scenario | Use MCP | Use SDK |
|----------|---------|---------|
| Creating a database collection during development | Appwrite MCP | — |
| Reading data in a Server Component | — | `node-appwrite` |
| Exploring your Figma design before coding | Figma MCP | — |
| Rendering a Figma-inspired component in your app | — | React + shadcn/ui |
| Deploying to preview | Vercel MCP or `deploy-to-vercel` skill | — |
| Looking up the latest Next.js API | Context7 MCP | — |
| Debugging a production error | Sentry MCP | — |

**Simple rule**: MCP is for **development workflow** (what you do while building). SDK is for **application code** (what runs in production).

---

## 6. Every File and Folder Explained

### Root Files

| File | What it does | Do you edit it? |
|------|-------------|-----------------|
| `CLAUDE.md` | **The brain.** Claude reads this every session. Contains stack, rules, workflow, quality standards, MCP servers, and doc references. | **Yes** — customise for your project |
| `.env.example` | Template for environment variables. | **Yes** — add new vars as needed |
| `.env.local` | Your actual secrets. Never committed to Git. | **Yes** — fill in real values |
| `.gitignore` | Files Git ignores. | Rarely |

### `.claude/` — Claude Code Configuration

| Path | What it does |
|------|-------------|
| `settings.json` | Permissions (which tools Claude can use), deny rules (can't read `.env`), hooks (auto-runs TypeScript check after file edits) |
| `commands/commit.md` | `/commit` — cleanup → security check → test → commit |
| `commands/feature.md` | `/feature` — review lessons → research (with MCP tools) → plan → wait for approval |
| `commands/fix.md` | `/fix` — autonomous debugging → root cause → regression test |
| `commands/pr.md` | `/pr` — five-lens review → PR description |
| `commands/catchup.md` | `/catchup` — reload lessons → tasks → git → health check |
| `skills/plan/SKILL.md` | Auto-triggers on "plan this" — five-lens planning |
| `skills/review/SKILL.md` | Auto-triggers on "review this" — five-lens code review |
| `skills/catchup/SKILL.md` | Auto-triggers on "where were we" — context recovery |

### `docs/` — Documentation (read on demand to save tokens)

| File | When Claude reads it |
|------|---------------------|
| `ARCHITECTURE.md` | Structural changes, new features, MCP integration |
| `CONVENTIONS.md` | Coding patterns, shadcn rules, payment integration |
| `SECURITY.md` | Any security work (24 numbered rules) |
| `security-playbook.md` | Deep security work (1,400 lines, 100+ rules) |
| `PAYMENTS.md` | Payment implementation (Stripe/PayPal/TNG) |
| `runbooks/mcp-setup.md` | MCP server setup and troubleshooting |
| `decisions/` | Architecture Decision Records |

### `src/` — Application Code

| Path | What goes here |
|------|---------------|
| `app/` | Next.js App Router — pages, layouts, loading, error |
| `app/(auth)/` | Login, register pages |
| `app/(dashboard)/` | Protected pages |
| `app/api/webhooks/` | Stripe, PayPal, TNG webhook handlers |
| `components/ui/` | shadcn primitives (CLI-managed — never create manually) |
| `components/features/` | Your custom components built from ui/ primitives |
| `lib/appwrite/` | Appwrite SDK setup (server.ts, client.ts, config.ts, types.ts) |
| `lib/payments/` | Payment provider setup (stripe/, paypal/, tng/) |
| `lib/env.ts` | Zod-validated environment variables |
| `lib/errors.ts` | Custom error classes |
| `lib/utils.ts` | Utility functions (cn, formatDate) |
| `actions/` | Server Actions by domain (auth.ts, projects.ts) |
| `hooks/` | Custom React hooks (use-auth.ts) |
| `providers/` | Context providers (QueryClientProvider) |
| `types/` | Shared TypeScript types |

### `tasks/` — Task Tracking

| File | Purpose |
|------|---------|
| `todo.md` | Claude writes plans here. Progress tracked with checkboxes. |
| `gotchas.md` | Lessons from mistakes. Reviewed at session start. |

### `tools/` — Automation

| Folder | What goes here |
|--------|---------------|
| `scripts/` | Automation scripts (setup-appwrite, seed-data) |
| `prompts/` | Reusable prompt templates for common Claude tasks |

---

## 7. How to Start a New Project

1. **Set up** — follow section 4 and 5 above
2. **Describe your requirements** to Claude Code
3. **Claude plans first** — writes to `tasks/todo.md`, waits for approval
4. **Claude uses MCP tools** — creates Appwrite collections via MCP, reads Figma designs via MCP, looks up latest docs via Context7
5. **Claude builds** — Appwrite → shadcn → Server → Client → Tests
6. **Claude verifies** — runs tests, asks "would a staff engineer approve?"
7. **You iterate** — corrections logged in `tasks/gotchas.md`

---

## 8. How Claude Code Uses These Files

**Loaded every session** (~250 lines total): `CLAUDE.md` + sub-directory `CLAUDE.md` files + skill descriptions

**Loaded on demand**: `docs/` files only when relevant (saves tokens)

**Written to**: `tasks/todo.md` (plans), `tasks/gotchas.md` (lessons), your source code

**MCP tools**: Called as needed — Appwrite MCP for DB operations, Figma MCP for design context, Context7 for latest docs, Vercel MCP for deployments

---

## 9. Daily Workflow

```
Start: claude → /catchup
Build: /feature [desc] → approve plan → Claude builds with MCP tools
Fix: /fix [desc] → autonomous
Commit: /commit → quality gate
PR: /pr → five-lens review
Context full: /clear → /catchup
```

---

## 10. Slash Commands Reference

| Command | What it does |
|---------|-------------|
| `/commit` | Cleanup → security → test → commit |
| `/feature [desc]` | Lessons → research (MCP tools) → plan → approve → build |
| `/fix [desc]` | Reproduce → root cause → fix → regression test |
| `/pr` | Quality gate → five-lens review → PR description |
| `/catchup` | Lessons → tasks → git → health |

---

## 11. Auto-Invoked Skills Reference

| Skill | Triggers on | What it does |
|-------|------------|-------------|
| `plan` | "plan this", "how should I build", complex tasks | Five-lens planning → `tasks/todo.md` → wait for approval |
| `review` | "review", "check for bugs", "audit" | Five-lens review with playbook rule IDs |
| `catchup` | "where were we", "what was I working on" | Full context reload |

---

## 12. Adding Your Requirements and Documents

- **PRDs/specs** → `docs/` folder, reference in `CLAUDE.md`
- **Figma designs** → use Figma MCP (`get_design_context`) directly
- **API docs** → `docs/` or use Context7 MCP for latest docs
- **Brand guidelines** → `docs/BRAND.md` + `design` and `brand-voice` plugins

---

## 13. Customising for Your Project

**Customise**: `CLAUDE.md` (project name, stack) · `docs/ARCHITECTURE.md` (collections, data flow) · `.env.example` (your vars) · `.claude/settings.json` (permissions)

**Don't change**: `security-playbook.md` · workflow orchestration rules · quality gate in `/commit`

**MCP**: Add/remove servers based on your services — see `docs/runbooks/mcp-setup.md`

---

## 14. Maintaining Over Time

- **Weekly**: review `tasks/gotchas.md`, run `npm audit`, prune CLAUDE.md
- **New feature**: document collections in ARCHITECTURE.md, create ADRs
- **New MCP**: add via `claude mcp add`, document in `docs/runbooks/mcp-setup.md`

---

## 15. FAQ and Troubleshooting

**Claude ignores rules** → CLAUDE.md may be too long. Keep under 120 lines. Move critical rules higher.

**Same mistake repeated** → Add to `tasks/gotchas.md` with a clear rule. If persistent, promote to CLAUDE.md.

**High token usage** → Check `claude mcp list` — reduce Appwrite MCP flags (each adds tools). Use `/clear` + `/catchup` to reset context.

**MCP server won't start** → Check `uv` is installed (`uvx --version`). Check env vars in the MCP config. See `docs/runbooks/mcp-setup.md`.

**"Can't resolve 'os'"** → `node-appwrite` imported in a `"use client"` file. Use `appwrite` (browser) package instead.

**`redirect()` fails silently** → It's inside a `try/catch`. Move it outside.

**Figma MCP not responding** → Reconnect via `claude mcp remove figma && claude mcp add --transport http figma https://mcp.figma.com/mcp`

**Appwrite MCP "authentication failed"** → API key expired or wrong scopes. Regenerate in Appwrite Console.

---

## Resources

- [Claude Code Docs](https://docs.anthropic.com/en/docs/claude-code/overview) · [MCP Docs](https://code.claude.com/docs/en/mcp)
- [Appwrite MCP Setup](https://appwrite.io/docs/tooling/mcp/claude-code) · [Appwrite Docs](https://appwrite.io/docs)
- [Figma MCP](https://mcp.figma.com) · [Next.js Docs](https://nextjs.org/docs/app) · [shadcn/ui](https://ui.shadcn.com)
- [Stripe Docs](https://docs.stripe.com) · [PayPal Developer](https://developer.paypal.com) · [TNG Mini Program](https://miniprogram.tngdigital.com.my/docs)
