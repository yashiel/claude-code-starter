---
name: catchup
description: Context recovery — gotchas review, task state, git state, health check. Use at session start or after /clear.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *), Read, Glob, Grep
---

1. Read `tasks/gotchas.md` — apply lessons this session
2. Read `tasks/todo.md` — current task progress
3. Git: `status`, `diff --stat`, `log --oneline -5`, uncommitted changes
4. Context: CLAUDE.md, relevant config files
5. Health: `npx tsc --noEmit | tail -10`, `npm test | tail -20`
6. Summary: branch, done/remaining, blockers, key gotchas
