---
name: plan
description: Five-lens planning. Writes to tasks/todo.md. STOP if sideways. Reviews gotchas first. For 3+ step tasks.
allowed-tools: Read, Glob, Grep
---

Review `tasks/gotchas.md` first. Read CLAUDE.md + relevant docs.

Plan through 5 lenses:
1. **Architect** — data model, component tree, data flow, scale (10x/100x)
2. **Security** — attack vectors per endpoint (playbook rules), auth, input validation, permissions
3. **QA** — worst+best case tests, auth/payment/error edge cases
4. **UX** — loading→content→success/error states, mobile, a11y, shadcn patterns
5. **Code** — beginner-readable? reusable utils? <300 lines? self-documenting names?

Write plan to `tasks/todo.md` with checkable items + verification steps.
Estimate: S/M/L. **STOP — wait for approval.**

Execute after approval: Appwrite → shadcn add → Server → Client → Tests.
If sideways: **STOP and re-plan.**
After: document results, capture lessons → `tasks/gotchas.md`.
