# MCP Server Setup Guide

> Connect Claude Code to external services via Model Context Protocol.

## Required MCP Servers

### MiHCM Design System — component search, token lookup, code review (MANDATORY)
```bash
claude mcp add --transport http --scope project mihcm https://designsystem.mihcm.com/mcp
```
20+ tools including: `mihcm_search_components`, `mihcm_get_component`, `mihcm_list_tokens`, `mihcm_validate_descriptor`, `mihcm_review_frontend_snippet`, `mihcm_check_contract`, `mihcm_check_client_server_boundary`, `mihcm_get_agent_setup`.

### Context7 — latest docs for ANY library
```bash
claude mcp add --transport http context7 https://mcp.context7.com/mcp
```

### Figma — design-to-code, screenshots, diagrams
```bash
claude mcp add --transport http figma https://mcp.figma.com/mcp
```

### Vercel — deployments and toolbar
```bash
claude mcp add --transport http vercel https://mcp.vercel.com
```

## Optional MCP Servers (add as needed)

### Sentry — error monitoring
```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

### Stripe — payment management
```bash
claude mcp add --transport http stripe https://mcp.stripe.com/mcp
```

### GitHub — issues, PRs, repos
```bash
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github
```

## MiHCM IDE Integration

### Cursor
One-click install from MiHCM design system docs, or add `.cursor/rules/mihcm-cursor.mdc`.

### VS Code
One-click MCP install from MiHCM agent tooling page.

### MiHCM Skill Packs (download from design system docs)
1. `mihcm-design-system` — component selection, token rules, AI-UI validation
2. `mihcm-frontend-build` — Next.js/Vite/Expo setup with theme enforcement
3. `mihcm-component-review` — accessibility, docs, and release audits
4. `mihcm-brand` — logo variants, marketing typography, asset governance

## Verify Setup
```bash
claude mcp list        # List all configured servers
claude mcp remove <n>  # Remove a server
```

## Scoping
- **Project-level**: `claude mcp add --scope project ...` (stored in `.claude/`)
- **User-level**: `claude mcp add --scope user ...` (stored in `~/.claude/`)

## Troubleshooting
- `npx ENOENT` → ensure Node.js is installed
- MCP tools not showing → restart Claude Code session
- MiHCM MCP requires no auth (read-only metadata server)
