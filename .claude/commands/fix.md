---
description: Autonomous fix — just fix it, root cause not symptoms, regression test, capture lessons.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *), Read, Glob, Grep
argument-hint: [bug or failing test]
---

Just fix it. No hand-holding. Root cause, not symptoms.

1. Reproduce — run test, capture error + stack trace
2. Security? — check playbook. If vulnerability: critical, fix now.
3. Common causes — async client component · redirect in try/catch · hydration mismatch · auth 401/403 · shadcn not installed · missing env var
4. Fix — minimal, elegant, beginner-readable. New security issues?
5. Test — regression (worst-case) + happy path (best-case). Full suite: `tsc && lint && test`.
6. Commit — `fix(scope): description`. Lessons → `tasks/gotchas.md`.
