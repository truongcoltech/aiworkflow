# generate-tasks.md — V9.4

## TRIAGE (run first — before any context load)

Classify the change before doing anything else. Do not load context until classification is done.

### Classification signals

| Signal | Weight |
|---|---|
| Blast radius — how many systems / modules / teams affected | **Primary** |
| Contract impact — does a public interface / shared type change? | **Primary** |
| Validation clarity — is pass/fail unambiguous without running the system? | **Primary** |
| File count | Supporting hint |
| LOC | Supporting hint |

### Classification levels

| Level | Criteria | Output |
|---|---|---|
| **TRIVIAL** | Single file, no public contract change, clear validation, low blast radius | Direct fix — no task file |
| **SIMPLE** | ≤2 files, no cross-module contract, clear path, low blast radius | Task note: TASK + DONE WHEN only |
| **STANDARD** | Cross-file, design decision, public contract changed, or validation unclear | Full task file |
| **EPIC** | Multi-session, multiple modules, new architecture pattern | Full task file + memory file |

### Safety override

Force upgrade to STANDARD regardless of above classification when touching:
- auth / session / token handling
- payment / billing logic
- database migrations
- tenant / workspace isolation
- infra / runtime config
- shared contracts (types or interfaces exported across modules)

### Context budget per classification

| Level | Load these files | Approx tokens |
|---|---|---|
| TRIVIAL | AGENTS.md golden rules + grep target file | ~1k |
| SIMPLE | AGENTS.md + `.ai/skills/<module>.md` | ~2–3k |
| STANDARD | AGENTS.md + CUTOFF.md + skills/ + ARCHITECTURE.md (if new resource) | ~5–8k |
| EPIC | Full read order + memory context | ~10–15k |

**Standards:** all levels — load applicable standards per AGENTS.md standards section before implementation. Standards tokens are in addition to the budget above.

---

## TASK
Convert a requirement into task files + execution plan.

---

## Context read order (STANDARD and EPIC only)

1. `AGENTS.md`
2. `docs/CUTOFF.md`
3. `.ai/SKILLS-TODO.md` — check for ❓ rows before starting
4. `.ai/skills/{module}.md` — if exists (skip source scan)
5. `docs/modules/{module}/` — only if in CUTOFF.md AND no skill file
6. `docs/ARCHITECTURE.md` — only if task involves new resource/endpoint/module

**TRIVIAL:** Load AGENTS.md golden rules + grep target file only.
**SIMPLE:** Load AGENTS.md + relevant `.ai/skills/{module}.md` only. Skip steps 2–6.

**If SKILLS-TODO.md has ❓ rows needed for this task:**
Stop. Ask human. Fill the row. Update AGENTS.md. Then continue.

---

## Pattern matching (STANDARD / EPIC only — before writing any task file)

| Task involves | Check ARCHITECTURE.md for |
|---|---|
| New resource / page | "Add a new \<resource\>" checklist |
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

## Task file formats

### TRIVIAL — no task file
Implement directly. Log in commit message only.

### SIMPLE — task note (2 sections only)

```markdown
## TASK
<one focused logical change — imperative sentence>

## DONE WHEN
- [ ] <condition>
- [ ] Compiles without errors
- [ ] No files outside stated scope modified
```

### STANDARD / EPIC — full task file

```markdown
## TASK
<one focused logical change — imperative sentence>
Classification: STANDARD | EPIC

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
- [ ] Standards validated: all applicable gates in `.ai/standards/definition-of-done.md` checked
- [ ] DOC UPDATE completed

## DOC UPDATE
<!-- Apply trigger matrix below — do not default to "update all" -->
<see doc trigger matrix — state each doc path + what to update, or "none required">

## COMMIT
type(scope): subject

- what changed and why
- key invariant enforced

Breaking: none
Migration: none
```

---

## Doc trigger matrix

**Meta-rule (apply this first):** Update docs only when the change affects how future humans or agents understand, navigate, or safely modify the system.

| Change type | `CUTOFF.md` date | `skills/<module>.md` | `ARCHITECTURE.md` | `DECISIONS.md` |
|---|---|---|---|---|
| Bug fix | ❌ | ❌ | ❌ | ❌ |
| New endpoint | ✅ | ✅ if interface changed | ❌ | ❌ |
| New module | ✅ | ✅ new stub | ✅ | optional |
| New arch pattern / decision | ❌ | ❌ | ✅ | ✅ |
| Config / env change | ✅ | ❌ | ❌ | optional |
| Refactor (no contract change) | ❌ | ❌ | ❌ | ❌ |

When in doubt: apply the meta-rule above — the table is a fast-path, not the final word.

---

## Execution plan (mandatory output for STANDARD / EPIC after task files are created)

Always output this block in chat:

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

Create `.ai/memory/{slug}-context.md` for EPIC work:

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
- Triage before any context load — no exceptions
- TRIVIAL: implement directly, no task file
- SIMPLE: 2-section task note only, no full task file
- Safety override list is non-negotiable — always upgrade to STANDARD
- No executor field in task files
- STANDARD/EPIC: output file paths + execution plan in chat, never task content
- No negative instructions in STEPS
- No full repo scan
- GOAL = expected outcome only, no implementation details
