# Multi-Agent Orchestration

> Specialised agents dispatched automatically by task type. **Agent-First Development** — each agent has a focused role, clear inputs/outputs, structured handoff reports.
> **Karpathy alignment**: agents are fallible interns. Human retains taste, judgment, system comprehension. Always review agent output.

## Agent Roster

| Agent | Purpose | Dispatch when |
|-------|---------|---------------|
| **Planner** (opus) | Requirements analysis, implementation phases, risk assessment | Any 3+ step feature or complex task |
| **Architect** (opus) | System design, scalability review, ADR creation | Architectural decisions, new system boundaries |
| **Researcher** | Search MiHCM MCP, npm, skills, GitHub for existing solutions | Before ANY new implementation |
| **Code Reviewer** | Code quality, MiHCM compliance, no hardcoded values | After any code written/modified |
| **Security Reviewer** | Vulnerability detection, OWASP checks, secret scanning | Auth/payment/PII code, pre-commit |
| **Build Resolver** | Fix build/type/lint failures | Verification loop fails |
| **Refactor Cleaner** | Dead code removal, pattern consolidation | Post-feature cleanup |
| **Design Researcher** | Research real product UIs, query MiHCM MCP | Before ANY UI work |
| **Design Auditor** | MiHCM compliance, no hardcoded values, visual quality | After any UI component created |
| **A11y Reviewer** | WCAG compliance, keyboard nav, screen reader | Every UI change |
| **TDD Guide** | Enforce RED-GREEN-REFACTOR cycle | Bug fixes and new features |
| **E2E Runner** | Playwright end-to-end execution | Critical user flows changed |
| **Doc Updater** | Update docs, codemaps, MEMORY.md | After significant changes |

## Orchestration Chains

| Task | Chain |
|------|-------|
| Feature | Planner → Researcher → [Design Researcher + MiHCM MCP if UI] → TDD Guide → Code Reviewer → Security Reviewer → Design Auditor |
| Bug fix | Planner → TDD Guide → Code Reviewer → [Security Reviewer if auth/payment] |
| Refactor | Architect → Code Reviewer → Refactor Cleaner → TDD Guide (verify no regressions) |
| UI component | Design Researcher (+ MiHCM MCP search) → Planner → TDD Guide → Code Reviewer → Design Auditor → A11y Reviewer |
| Security audit | Security Reviewer → Code Reviewer → Architect (review boundaries) |

Chains double as the automatic dispatch rules: the trigger column of the roster says when each agent fires; new dependency considered → Researcher; UI created/modified → Design Auditor + A11y Reviewer + MiHCM MCP always.

## MiHCM-First Protocol (Design Researcher)
Before building ANY UI component: `mihcm_search_components` → `mihcm_get_component` (props, variants, examples) → `mihcm_list_tokens` → decide: **use directly / compose / extend / request** — never build a custom primitive. Request = handoff doc from `docs/component-requests/TEMPLATE.md` + MiHCM placeholder in UI (full protocol: `CLAUDE.md > MiHCM-First Rule`).

## Search-First Protocol (Researcher)
1. Define need (functionality + constraints)
2. Search: MiHCM MCP → npm → skills → GitHub (in order)
3. Evaluate: functionality, maintenance, size, license
4. Decide: Adopt > Extend > Compose > Build
Anti-patterns: jumping to code, ignoring MiHCM MCP, dependency bloat.

## Handoff Document Format
```markdown
## Agent: [name]
### Context — what was analysed and why
### Findings — each with severity: critical/high/medium/low
### MiHCM Compliance — components used; hardcoded values found (or "none")
### Files Modified/Reviewed — `src/path/file.ts` — what was done
### Open Questions — for next agent or user
### Recommendations — action items
```

## Parallel Execution Rules
Independent agents run simultaneously (e.g., Security Reviewer + Design Auditor) · dependent agents sequential (Planner before TDD Guide) · max 3-5 parallel for complex features · context isolation: each agent gets only the files/context it needs.

## Iterative Retrieval (subagent context)
DISPATCH broad (glob, grep key terms) → EVALUATE relevance → REFINE search → LOOP max 3 cycles. 3 high-relevance files > 10 mediocre ones.
