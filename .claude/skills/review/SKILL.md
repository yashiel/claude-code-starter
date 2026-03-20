---
name: review
description: Five-lens review — security (playbook IDs), QA (adversarial), architect (scale), UX (a11y), code (simplicity+elegance). Challenges own work.
allowed-tools: Bash(git *), Bash(npm run *), Bash(npx *), Read, Glob, Grep
---

Challenge own work first. "Is there a cleaner way?" "Would a staff engineer approve?"

**Security** — AUTH/AUTHZ/INJ/PAY/AW rules from playbook. RSC boundary violations. Secrets in client. Webhook sigs.
**QA** — worst+best case tests exist? Auth/payment edge cases? Error boundaries + loading + empty states? `npm test` passes?
**Architect** — waterfalls? Correct fetch pattern? TanStack only where needed? Separation of concerns?
**UX** — mobile-first? WCAG 2.1 AA? shadcn patterns correct? Skeletons not spinners?
**Code** — dead code? duplicates? beginner-readable? <300 lines? magic values? Elegance: hacky → "implement the elegant solution"

Output per finding: Severity · Rule ID · File:line · Issue (1-2 sentences) · Fix
Update `tasks/gotchas.md` with new patterns discovered.
