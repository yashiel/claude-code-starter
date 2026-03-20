---
description: Quality gate — cleanup, security check, verify, commit. Never done without proof.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *)
---

1. **Cleanup** — `git diff` review. Remove console.log/debugger/dead code/unused imports/duplicates. Beginner-readable? Elegant?
2. **Security** — no secrets (grep sk_/password/api_key). No node-appwrite in client. Inputs validated (zod). Auth on actions. Amounts server-side. redirect outside try/catch.
3. **Verify** — `npx tsc --noEmit` · `npm run lint` · `npm test` — all clean. "Staff engineer approved?"
4. **Commit** — stage relevant files only. `type(scope): description`. Use $ARGUMENTS if provided.
5. **Post** — update `tasks/todo.md`. If corrections needed → `tasks/gotchas.md`.
