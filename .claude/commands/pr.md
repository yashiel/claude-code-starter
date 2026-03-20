---
description: Expert PR — quality gate + five-lens review + structured description.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *), Read, Glob, Grep
---

1. Gate: `tsc --noEmit` · `lint` · `test` · `build` — all clean
2. Security: playbook Pre-Commit §22 · grep secrets · auth on endpoints · inputs validated
3. Quality: dead code removed · beginner-readable · <300 lines
4. QA: worst+best case tests · payment edge cases · error boundaries
5. UX: semantic tokens · responsive · accessible

PR description: What · Why · How · Security checklist · Testing (worst+best+manual) · Changes · Appwrite/shadcn/Payment changes · Screenshots
Split if >400 lines.
