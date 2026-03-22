# Multi-Agent Orchestration

> Specialised agents dispatched automatically based on task type.
> Inspired by [everything-claude-code](https://github.com/affaan-m/everything-claude-code) agent patterns.

## Core Principle
**Agent-First Development** — delegate to specialised subagents instead of doing everything in one context. Each agent has a focused role, clear inputs/outputs, and hands off context via structured reports.

## Agent Roster

### Planning & Architecture
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Planner** (opus) | Requirements analysis, implementation phases, risk assessment | Any 3+ step feature or complex task |
| **Architect** (opus) | System design, scalability review, ADR creation | Architectural decisions, new system boundaries |
| **Researcher** | Search for existing solutions, evaluate packages, read docs | Before ANY new implementation |

### Implementation & Quality
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Code Reviewer** | Code quality, maintainability, patterns, dead code detection | After any code is written/modified |
| **Security Reviewer** | Vulnerability detection, OWASP checks, secret scanning | Auth/payment/PII code, pre-commit |
| **Build Resolver** | Fix build errors, type errors, lint failures | When verification loop fails |
| **Refactor Cleaner** | Dead code removal, pattern consolidation | Post-feature cleanup |

### Design & UX
| Agent | Purpose | When to Dispatch |
|-------|---------|-----------------|
| **Design Researcher** | Research real product UIs, gather design references | Before ANY UI work |
| **Design Auditor** | AI Slop Detection, visual quality review | After any UI component is created |
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

Dispatch agents in sequence. Each agent produces a handoff document consumed by the next.

### Feature Development
```
Planner → Researcher → [Design Researcher if UI] → TDD Guide → Code Reviewer → Security Reviewer → Design Auditor
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
Design Researcher → Planner → TDD Guide → Code Reviewer → Design Auditor → A11y Reviewer
```

### Security Audit
```
Security Reviewer → Code Reviewer → Architect (review boundaries)
```

## Handoff Document Format

Every agent produces a structured handoff:

```markdown
## Agent: [name]
### Context
What was analysed and why.

### Findings
- Finding 1 (severity: critical/high/medium/low)
- Finding 2

### Files Modified/Reviewed
- `src/path/file.ts` — what was done

### Open Questions
- Question for next agent or user

### Recommendations
- Action item 1
- Action item 2
```

## Parallel Execution Rules

- **Independent agents** run simultaneously (e.g., Security Reviewer + Design Auditor)
- **Dependent agents** run sequentially (e.g., Planner must finish before TDD Guide starts)
- **Max parallel**: 3-5 agents for complex features
- **Context isolation**: each agent gets only the files/context it needs

## Automatic Dispatch Rules

| Trigger | Agent(s) |
|---------|----------|
| Complex feature request | Planner (first) |
| Code just written/modified | Code Reviewer |
| Bug fix or new feature | TDD Guide |
| Architectural decision needed | Architect |
| Auth, payment, or PII code touched | Security Reviewer |
| UI component created/modified | Design Auditor + A11y Reviewer |
| New dependency considered | Researcher |
| Build/type/lint errors | Build Resolver |
| Post-feature cleanup | Refactor Cleaner |

## Search-First Protocol (Researcher Agent)

Before writing ANY new utility, helper, or integration:

1. **Define need** — what functionality + constraints
2. **Parallel search** — npm registry, MCP tools, available skills, GitHub
3. **Evaluate candidates** — functionality, maintenance, community, docs, license, size
4. **Decision matrix**:
   - **Adopt** — use the package directly (best option)
   - **Extend** — install + thin wrapper for project conventions
   - **Compose** — combine 2-3 focused packages
   - **Build** — custom implementation, informed by research findings
5. **Anti-patterns**: jumping to code without checking, ignoring MCP tools, over-customising library wrappers, dependency bloat

## Iterative Retrieval (for Subagent Context)

When a subagent needs to understand the codebase:

1. **DISPATCH** — start broad (glob patterns, grep for key terms)
2. **EVALUATE** — assess relevance of what was found
3. **REFINE** — narrow search based on discovered terminology/patterns
4. **LOOP** — max 3 cycles. 3 high-relevance files > 10 mediocre ones.

First cycle reveals codebase naming conventions. Second cycle uses those conventions. Third cycle fills remaining gaps.
