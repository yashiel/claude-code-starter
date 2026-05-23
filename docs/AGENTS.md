# Multi-Agent Orchestration

> Specialised agents dispatched automatically based on task type.

## Core Principle
**Agent-First Development** — delegate to specialised subagents. Each agent has a focused role, clear inputs/outputs, and hands off context via structured reports.

**Karpathy Alignment**: Agents are fallible interns. Human retains taste, judgment, system comprehension. Always review agent output.

## Agent Roster

### Planning & Architecture
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Planner** (opus) | Requirements analysis, implementation phases, risk assessment | Any 3+ step feature or complex task |
| **Architect** (opus) | System design, scalability review, ADR creation | Architectural decisions, new system boundaries |
| **Researcher** | Search MiHCM MCP, npm, skills, GitHub for existing solutions | Before ANY new implementation |

### Implementation & Quality
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Code Reviewer** | Code quality, MiHCM compliance, no hardcoded values | After any code is written/modified |
| **Security Reviewer** | Vulnerability detection, OWASP checks, secret scanning | Auth/payment/PII code, pre-commit |
| **Build Resolver** | Fix build errors, type errors, lint failures | When verification loop fails |
| **Refactor Cleaner** | Dead code removal, pattern consolidation | Post-feature cleanup |

### Design & UX
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Design Researcher** | Research real product UIs, query MiHCM MCP for components | Before ANY UI work |
| **Design Auditor** | MiHCM compliance, no hardcoded values, visual quality | After any UI component is created |
| **A11y Reviewer** | WCAG compliance, keyboard nav, screen reader testing | Every UI change |

### Testing
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **TDD Guide** | Enforce RED-GREEN-REFACTOR cycle | Bug fixes and new features |
| **E2E Runner** | Playwright end-to-end test execution | Critical user flows changed |

### Documentation
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Doc Updater** | Update docs, codemaps, MEMORY.md | After significant changes |

## Orchestration Chains

### Feature Development
```
Planner → Researcher → [Design Researcher + MiHCM MCP if UI] → TDD Guide → Code Reviewer → Security Reviewer → Design Auditor
```

### Bug Fix
```
Planner → TDD Guide → Code Reviewer → [Security Reviewer if auth/payment]
```

### Refactor
```
Architect → Code Reviewer → Refactor Cleaner → TDD Guide (verify no regressions)
```

### UI Component
```
Design Researcher (+ MiHCM MCP search) → Planner → TDD Guide → Code Reviewer → Design Auditor → A11y Reviewer
```

### Security Audit
```
Security Reviewer → Code Reviewer → Architect (review boundaries)
```

## MiHCM-First Protocol (Design Researcher Agent)

Before building ANY UI component:

1. **Search MiHCM MCP** — `mihcm_search_components` for existing components
2. **Get details** — `mihcm_get_component` for props, variants, examples
3. **Check tokens** — `mihcm_list_tokens` for available design tokens
4. **Decision**:
   - **Use directly** — MiHCM component fits requirements (best option)
   - **Compose** — combine MiHCM components into a feature composite
   - **Extend** — wrap MiHCM component with domain-specific props
   - **Request** — no MiHCM component exists → create handoff doc at `docs/component-requests/[ComponentName].md` (see CLAUDE.md > Component Request Protocol). Use a MiHCM placeholder (Card/Alert with "Component pending" message) in the UI while the design team builds it.
   - **Never**: build a custom UI primitive from scratch

## Automatic Dispatch Rules

| Trigger | Agent(s) | MCP |
|---------|----------|-----|
| Complex feature request | Planner (first) | — |
| Code just written/modified | Code Reviewer | — |
| Bug fix or new feature | TDD Guide | — |
| Architectural decision needed | Architect | — |
| Auth, payment, or PII code touched | Security Reviewer | — |
| UI component created/modified | Design Auditor + A11y Reviewer | MiHCM MCP (always) |
| New UI work starting | Design Researcher | MiHCM MCP (always) |
| New dependency considered | Researcher | — |
| Build/type/lint errors | Build Resolver | — |
| Post-feature cleanup | Refactor Cleaner | — |

## Search-First Protocol (Researcher Agent)

1. **Define need** — what functionality + constraints
2. **Search** — MiHCM MCP → npm → skills → GitHub (in this order)
3. **Evaluate** — functionality, maintenance, size, license
4. **Decision**: Adopt > Extend > Compose > Build
5. **Anti-patterns**: jumping to code, ignoring MiHCM MCP, dependency bloat

## Handoff Document Format

```markdown
## Agent: [name]
### Context
What was analysed and why.

### Findings
- Finding 1 (severity: critical/high/medium/low)

### MiHCM Compliance
- Components used: [list]
- Hardcoded values found: [list or "none"]

### Files Modified/Reviewed
- `src/path/file.ts` — what was done

### Open Questions
- Question for next agent or user

### Recommendations
- Action item 1
```

## Parallel Execution Rules

- **Independent agents** run simultaneously (e.g., Security Reviewer + Design Auditor)
- **Dependent agents** run sequentially (e.g., Planner must finish before TDD Guide)
- **Max parallel**: 3-5 agents for complex features
- **Context isolation**: each agent gets only the files/context it needs

## Iterative Retrieval (for Subagent Context)

1. **DISPATCH** — start broad (glob patterns, grep for key terms)
2. **EVALUATE** — assess relevance
3. **REFINE** — narrow search based on discovered patterns
4. **LOOP** — max 3 cycles. 3 high-relevance files > 10 mediocre ones.
