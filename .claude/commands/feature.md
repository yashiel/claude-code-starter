---
description: Plan-first feature — review gotchas, research (use MCP tools), plan to todo.md, wait for approval, execute, capture lessons.
allowed-tools: Bash(git *), Bash(npx *), Read, Glob, Grep
argument-hint: [feature-description]
---

1. Review `tasks/gotchas.md` + CLAUDE.md
2. Research using MCP tools:
   - **Appwrite MCP** → check existing collections, schemas, indexes
   - **Figma MCP** → `get_design_context` if designs exist for this feature
   - **Context7 MCP** → lookup latest API docs for libraries being used
   - **Appwrite Docs MCP** → check latest Appwrite SDK patterns
   - Also: `npx shadcn@latest info/search`, existing code in config.ts, actions/, docs/
3. Branch: `feat/[name]` from main
4. Plan → `tasks/todo.md`: Appwrite setup (use MCP to create collections) · shadcn components · Server → Client · Security · Tests (worst+best) · UX states. **STOP — wait for approval.**
5. Execute: Appwrite MCP for schema → shadcn add → Server → Client → Tests. `tsc && lint && test` after each step.
6. If sideways: **STOP and re-plan.**
7. Verify: "Staff engineer approved?"
8. Lessons → `tasks/gotchas.md`
