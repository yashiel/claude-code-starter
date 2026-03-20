---
description: Autonomous fix — just fix it, root cause not symptoms, regression test, capture lessons.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *), Read, Glob, Grep
argument-hint: [bug or failing test]
---

Just fix it. No hand-holding. Root cause, not symptoms.

1. Reproduce — run test, capture error + stack trace
2. Security? — check playbook. If vulnerability: critical, fix now, ADR.
3. Common causes — node-appwrite in client · async client component · redirect in try/catch · hydration mismatch · Appwrite 401/403/404 · shadcn not installed · Stripe raw body · PayPal token expired · TNG signature/amount/U-status
4. Fix — minimal, elegant, beginner-readable. New security issues?
5. Test — regression (worst-case) + happy path (best-case). Full suite: `tsc && lint && test`.
6. Commit — `fix(scope): description`. Lessons → `tasks/gotchas.md`.
