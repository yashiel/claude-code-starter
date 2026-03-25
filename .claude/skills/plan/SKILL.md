---
name: plan
description: Eight-lens planning with persona protocol. Writes to tasks/todo.md. STOP if sideways. Reviews gotchas first. Auto-loads skills. For 2+ step tasks.
allowed-tools: Read, Glob, Grep
---

## Step 1: Context
Review `tasks/gotchas.md` + `MEMORY.md` + relevant docs.

## Step 2: Run All 8 Personas
For the task at hand, answer each persona's questions:

- [ ] **System Architect** — fits system? data flow? boundaries? scale 10x/100x?
- [ ] **Software Engineer** — clean? SOLID? error handling? edge cases? types?
- [ ] **Database Engineer** — schema? indexes? RLS? queries? migrations reversible?
- [ ] **QA Engineer** — what breaks? null? empty? 10k? unicode? concurrent? auth bypass?
- [ ] **Designer** — production-grade? researched 3-5 real examples? no AI slop?
- [ ] **Creative Director** — visual identity? typography hierarchy? color story? spacing?
- [ ] **UX Designer** — frictionless? cognitive load? feedback? error recovery? progressive disclosure?
- [ ] **Frontend Developer** — RSC? accessible? mobile-first? all states? performance?

Document conflicts between personas and how they're resolved.

## Step 3: Auto-Load Skills
Check CLAUDE.md skill matrix. Load ALL applicable skills for this task type. If UI is involved, load ALL design skills — no exceptions.

## Step 4: Write Plan
Write plan to `tasks/todo.md` with:
- Checkable items per implementation step
- Persona checklist results (from Step 2)
- Skills loaded (from Step 3)
- Verification steps (6-phase loop)
- Estimate: S/M/L

**STOP — wait for approval.**

## Step 5: Execute (after approval)
Appwrite → shadcn add → Server → Client → Tests.
If sideways: **STOP and re-plan.**

## Step 6: Close
- Document results in `tasks/todo.md`
- Capture lessons → `tasks/gotchas.md`
- Update `MEMORY.md` with session context
