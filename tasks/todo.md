# Task: Context optimization + auth documentation (2026-07-06)

## Plan
- [x] Fix stale shadcn/Appwrite refs (commands, skills, diagrams, scripts)
- [x] Lever 1: @docs imports → on-demand references (SECURITY.md stays loaded)
- [x] Lever 2: dedupe CLAUDE.md (merge library tables, move CR template, drop duplicate Principles)
- [x] Lever 3: compress AGENTS.md + ARCHITECTURE.md
- [x] Document IdentityServer4 + Entra ID auth (CLAUDE.md, ARCHITECTURE.md)
- [x] Update MEMORY.md

## Verify
- [x] Diff reviewed (docs/config only) · secret grep clean · tsc/lint/test N/A (scaffold, no package.json)

## Results
Always-loaded context 9.1k → 2.7k words (−70%). Every rule preserved exactly once; detail docs load on demand via read-when triggers. Auth flow documented: IdentityServer4 (Code + PKCE) + Entra ID federated, BFF holds tokens.

## Lessons
- ⚠️ IdentityServer4 EOL since Nov 2022 (successor Duende) — upstream MiHCM concern, raise with platform team if security review touches STS.
- Open: confirm auth topology (IdSrv4 as STS with Entra federated vs direct Entra/MSAL) — Auth Flow diagram assumes former.

## Component Requests
(none yet — template at docs/component-requests/TEMPLATE.md)
