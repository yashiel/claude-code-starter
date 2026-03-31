# Project Memory

> Cross-session context. Read this FIRST at every session start. Updated at every session end.

## Latest Session

**Date**: [DATE]
**What was done**:
- [Summary of work]

**Decisions made**:
- [Key decisions]

**Project state**:
- Scaffold only — no application code yet
- Branch: `main`

**What's next**:
- User to define PROJECT_NAME and one-line description
- Initialize Next.js app or copy starter into existing project
- Begin feature development

## Architecture Decisions

| Decision | Why | Date |
|----------|-----|------|
| 8-role persona protocol | Prevents tunnel vision across system, code, DB, QA, design, UX | - |
| Dual-repo publishing | Cloud providers only need deployable code; dev context stays separate | - |
| Multi-agent orchestration | Specialised agents > one-size-fits-all | - |
| Search-first: Adopt > Extend > Compose > Build | Never reinvent what exists | - |
| 6-phase verification loop | Build → TypeCheck → Lint → Tests → Security → Diff — all must pass | - |
| Auto-skill loading by task type | Eliminates forgotten skills — matrix maps task → skills | - |
| 7 Mermaid diagrams maintained | Architecture must be visual and current | - |

## Session History

| Date | Summary |
|------|---------|
