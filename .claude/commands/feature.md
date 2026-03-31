---
description: Plan-first feature — run 8 personas, auto-load skills, research, plan to todo.md, wait for approval, execute, capture lessons, update memory.
allowed-tools: Bash(git *), Bash(npx *), Read, Glob, Grep
argument-hint: [feature-description]
---

1. Review `tasks/gotchas.md` + `MEMORY.md` + `CLAUDE.md`

2. **Run 8 Personas** on this feature:
   - [ ] System Architect — data flow, boundaries, scale
   - [ ] Software Engineer — code quality, error handling, types
   - [ ] Database Engineer — schema, indexes, migrations
   - [ ] QA Engineer — what breaks, edge cases, adversarial inputs
   - [ ] Designer — production-grade, research 3-5 real examples
   - [ ] Creative Director — visual identity, typography, color, spacing
   - [ ] UX Designer — journey, cognitive load, feedback, a11y
   - [ ] Frontend Developer — RSC, mobile-first, states, performance

3. **Auto-load skills** per CLAUDE.md skill matrix

4. **Research** using MCP tools:
   - **Figma MCP** → `get_design_context` if designs exist
   - **Context7 MCP** → latest API docs for libraries being used
   - Also: `npx shadcn@latest info/search`, existing code, docs/

5. Branch: `feat/[name]` from main

6. **Plan** → `tasks/todo.md`:
   - Persona review results
   - Implementation steps with checkboxes
   - Skills loaded
   - Verification steps
   **STOP — wait for approval.**

7. Execute per plan. `tsc && lint && test` after each step.

8. If sideways: **STOP and re-plan.**

9. Run 6-phase Verification Loop

10. Lessons → `tasks/gotchas.md`

11. **Update `MEMORY.md`** — what was built, decisions, what's next
