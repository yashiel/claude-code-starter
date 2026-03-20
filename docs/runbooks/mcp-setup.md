# MCP Server Setup Guide

> Connect Claude Code to external services via Model Context Protocol.
> Run these commands in your terminal (where Claude Code runs).

## Required MCP Servers

### Appwrite API — manage databases, users, storage, functions
```bash
claude mcp add-json appwrite-api '{
  "command": "uvx",
  "args": ["mcp-server-appwrite", "--users", "--databases", "--storage", "--functions", "--teams"],
  "env": {
    "APPWRITE_PROJECT_ID": "your-project-id",
    "APPWRITE_API_KEY": "your-api-key",
    "APPWRITE_ENDPOINT": "https://cloud.appwrite.io/v1"
  }
}'
```
**Note**: Only `--databases` is enabled by default to save tokens. Add flags for APIs you need: `--users`, `--storage`, `--functions`, `--teams`, `--messaging`, `--locale`, `--avatars`.

### Appwrite Docs — access latest Appwrite documentation
```bash
claude mcp add-json appwrite-docs '{
  "command": "npx",
  "args": ["mcp-remote", "https://mcp-for-docs.appwrite.io"]
}'
```

### Figma — design-to-code, screenshots, metadata, design context
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI, connect via:
claude mcp add --transport http figma https://mcp.figma.com/mcp
```
**Tools**: `get_design_context` (primary for design-to-code), `get_screenshot`, `get_metadata`, `generate_diagram` (FigJam), `get_code_connect_map`, `add_code_connect_map`

### Vercel — deployments, toolbar threads
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI:
claude mcp add --transport http vercel https://mcp.vercel.com
```

### Sentry — error monitoring and debugging
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI:
claude mcp add --transport http sentry https://mcp.sentry.dev/mcp
```

### Context7 — up-to-date library documentation
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI:
claude mcp add --transport http context7 https://mcp.context7.com/mcp
```
**Use for**: Getting latest docs for any library (Next.js, React, Appwrite SDK, etc.)

### Gmail — email drafts, labels
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI:
claude mcp add --transport http gmail https://gmail.mcp.claude.com/mcp
```

### Indeed — job search, company data, resume
```bash
# Already available as cloud MCP in claude.ai
```

### Canva — graphics, marketing visuals
```bash
# Already available as cloud MCP in claude.ai
# For Claude Code CLI:
claude mcp add --transport http canva https://mcp.canva.com/mcp
```

## Optional MCP Servers (add as needed)

### Stripe — payment management (if not using SDK directly)
```bash
claude mcp add --transport http stripe https://mcp.stripe.com/mcp
```

### Slack — messaging, search
```bash
claude mcp add --transport http slack https://mcp.slack.com/mcp
```

### GitHub — issues, PRs, repos
```bash
claude mcp add --transport stdio github -- npx -y @modelcontextprotocol/server-github
```

## Verify Setup
```bash
# List all configured MCP servers
claude mcp list

# Remove a server
claude mcp remove <name>
```

## Scoping
- **Project-level**: `claude mcp add --scope project ...` (stored in `.claude/`)
- **User-level**: `claude mcp add --scope user ...` (stored in `~/.claude/`)

## Troubleshooting
- `uvx ENOENT` → install `uv`: `curl -LsSf https://astral.sh/uv/install.sh | sh`
- `npx ENOENT` → ensure Node.js is installed
- MCP tools not showing → restart Claude Code session
- Token budget exceeded → reduce enabled Appwrite API flags (each flag adds tools to context)
