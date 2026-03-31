# MCP Server Setup Guide

> Connect Claude Code to external services via Model Context Protocol.

## Recommended MCP Servers

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

### Sentry — error monitoring
```bash
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

## Optional MCP Servers (add as needed)

### Stripe — payment management
```bash
claude mcp add --transport http stripe https://mcp.stripe.com/mcp
```

### GitHub — issues, PRs, repos
```bash
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github
```

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
