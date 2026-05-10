# generate-tasks.md — V9.3

## TASK
Convert a requirement into task files + execution plan.

---

## Context read order (mandatory)

1. `AGENTS.md`
2. `docs/CUTOFF.md`
3. `.ai/SKILLS-TODO.md` — check for ❓ rows before starting
4. `.ai/skills/<module>.md` — if exists
5. `docs/modules/<module>/*` — only if in CUTOFF.md AND no skill file
6. `docs/ARCHITECTURE.md` — only if task involves new resource/endpoint/module

**If SKILLS-TODO.md has ❓ rows needed for this task:**
Stop. Ask human. Fill the row. Update AGENTS.md. Then continue.

---

## Pattern matching (before writing any task file)

| Task involves | Check ARCHITECTURE.md for |
|---|---|
| New resource / page | "Add a new <resource>" checklist |
| New API endpoint | Auth guards, validation, error handling pattern |
| New DB table | Migration + type registration pattern |
| New module | Module wiring / registration pattern |
| Bugfix | No pattern — scope to failing path only |

Every checklist step must appear in STEPS or be marked `N/A — <reason>`.

---

## Executor rule

Do NOT write executor in task files. Human decides at paste time.
Claude outputs an execution plan — human uses it to decide what to paste where.

Codex = all code edits (default).
Cline = shell required OR Codex quota exhausted.

---

## Granularity rule

One task = one logical change.
Merge when: same module, no inter-dependency, no shell needed.
Split when: different executor needed, true sequential dependency.

---

## Task file template

```markdown
## TASK
<one focused logical change — imperative sentence>

## PRIOR CONTEXT
<!-- Remove for first-session tasks -->
- context: .ai/memory/<feature-slug>-context.md
- done so far: <one-line summary>

## PATTERN
<!-- Runbook pattern from ARCHITECTURE.md, or "none" -->
none | Add a new <resource> | Add a new endpoint | ...

## CONTEXT
- files:
  - <entry-point path>
- docs:
  - .ai/skills/<module>.md

## GOAL
<expected outcome — from ACCEPTANCE CRITERIA>

## STEPS
<!-- Positive instructions only. No "don't do X". -->
1. grep/find to confirm paths before editing
2. ...

## DONE WHEN
- [ ] <condition from ACCEPTANCE CRITERIA>
- [ ] Compiles without errors
- [ ] Smoke test passes (new endpoints)
- [ ] Unit test passes (new business logic)
- [ ] No files outside CONTEXT modified
- [ ] All PATTERN steps completed or marked N/A
- [ ] DOC UPDATE completed

## DOC UPDATE
- `docs/CUTOFF.md` — update last-updated date
- `.ai/skills/<module>.md` — update if interface changed
- (or: none required)

## COMMIT
type(scope): subject

- what changed and why
- key invariant enforced

Breaking: none
Migration: none
```

---

## Execution plan (mandatory output after generating tasks)

Always output this block in chat after task files are created:

```
## Execution plan

### Codex — paste all at once (independent):
.ai/tasks/<module>/NNN-name.md
.ai/tasks/<module>/NNN-name.md

### Codex — paste after group above:
.ai/tasks/<module>/NNN-name.md

### Cline — after Codex done (shell required):
.ai/tasks/<module>/NNN-name.md

### Batch commit message:
type(scope): overall subject

- task 1 summary
- task 2 summary

Breaking: none
Migration: none
```

Rules: Codex groups first, Cline last. Independent tasks in same group.

---

## Epic memory file

Create `.ai/memory/<slug>-context.md` for multi-session work:

```markdown
# <Feature> — Context
Created-by: <first task ID>
Active-until: <last task ID> done
Owner: <module>
Auto-expire: <today + 14 days>

## State
## Decisions
## Tasks
| # | File | Status | Depends on |
|---|---|---|---|

## Next
```

---

## Hard constraints
- No executor field in task files
- No task content in chat — file paths + execution plan only
- No negative instructions in STEPS
- No full repo scan
- GOAL = expected outcome only, no implementation details
