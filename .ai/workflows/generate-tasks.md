# generate-tasks.md — V1.0

## ROLE CHECK (run before triage — architect only)

This workflow is for **ARCHITECT role only**.

If message intent is executor (implement / fix / build / write / refactor):
→ Stop. Do not generate task files. Load `exec-context.md` and implement directly.

If message intent is architect (design / plan / break down / analyze / review):
→ Continue to TRIAGE below.

---

## TRIAGE (run after role check — architect only)

Classify the change before doing anything else. Do not load context until classification is done.

### Classification signals

| Signal | Weight |
| --- | --- |
| Blast radius — how many systems / modules / teams affected | **Primary** |
| Contract impact — does a public interface / shared type change? | **Primary** |
| Validation clarity — is pass/fail unambiguous without running the system? | **Primary** |
| File count | Supporting hint |
| LOC | Supporting hint |

### Classification levels

| Level | Criteria | Output |
| --- | --- | --- |
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
| --- | --- | --- |
| TRIVIAL | .ai/AGENTS.md golden rules + grep target file | ~1k |
| SIMPLE | .ai/AGENTS.md + `.ai/skills/<module>.md` | ~2–3k |
| STANDARD | .ai/AGENTS.md + CUTOFF.md + skills/ + ARCHITECTURE.md (if new resource) | ~5–8k |
| EPIC | Full read order + memory context | ~10–15k |

**Standards:** all levels — load applicable standards per .ai/AGENTS.md standards section before implementation. Standards tokens are in addition to the budget above.

---

## TASK
Convert a requirement into task files + execution plan.

---

## Context read order (STANDARD and EPIC only)

1. `.ai/AGENTS.md`
2. `docs/CUTOFF.md`
3. `.ai/SKILLS-TODO.md` — check for ❓ rows before starting
4. `.ai/skills/{module}.md` — if exists (skip source scan)
5. `docs/modules/{module}/` — only if in CUTOFF.md AND no skill file
6. `docs/ARCHITECTURE.md` — only if task involves new resource/endpoint/module

**TRIVIAL:** Load .ai/AGENTS.md golden rules + grep target file only.
**SIMPLE:** Load .ai/AGENTS.md + relevant `.ai/skills/{module}.md` only. Skip steps 2–6.

**If SKILLS-TODO.md has ❓ rows needed for this task:**
Stop. Ask human. Fill the row. Update .ai/AGENTS.md. Then continue.

---

## Pattern matching (STANDARD / EPIC only — before writing any task file)

| Task involves | Check ARCHITECTURE.md for |
| --- | --- |
| New resource / page | "Add a new \<resource\>" checklist |
| New API endpoint | Auth guards, validation, error handling pattern |
| New DB table | Migration + type registration pattern |
| New module | Module wiring / registration pattern |
| Bugfix | No pattern — scope to failing path only |

Every checklist step must appear in STEPS or be marked `N/A — <reason>`.

---

## Executor rule

Do NOT write executor in task files. Human decides at run time.
Claude outputs an execution plan — human uses it to decide what to run where.

Executor = tool(s) listed in `{{AI_TOOLS}}` in .ai/AGENTS.md.
Shell runner = Cline or terminal — for shell-only steps.

## tasks/ naming convention

<!-- Determined by {{GIT_FLOW}} and {{TEAM_SIZE}} — filled by setup wizard -->

```text
{{TASKS_PATH_CONVENTION}}
```

**Branch slug rule:** take current git branch name, replace `/` with `-`.
Example: branch `feat/PROJ-42-auth-refresh` → slug `feat-PROJ-42-auth-refresh`
→ task file: `tasks/feat-PROJ-42-auth-refresh/001-add-refresh-endpoint.md`

**Hard rules:**
- One task file per logical change — never bundle unrelated changes
- Task files live in `.ai/tasks/` — never in project source directories
- Do not touch another branch's task directory

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
- [ ] No claim made about existing code without citing file:line
- [ ] Skill files loaded: 2+ key claims grep-verified in source (or flagged ⚠️ stale)
- [ ] If interface changed: skill file for affected module updated or rewritten
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

| Change type | `CUTOFF.md` date | `skills/{module}.md` | `ARCHITECTURE.md` | `DECISIONS.md` | `LESSONS.md` | `exec-context.md` |
| --- | --- | --- | --- | --- | --- | --- |
| Bug fix | ❌ | ❌ | ❌ | ❌ | ✅ if dangerous pattern | ❌ |
| New endpoint | ✅ | ✅ if interface changed | ❌ | ❌ | ❌ | ❌ |
| New module | ✅ | ✅ new stub | ✅ | optional | ❌ | ❌ |
| New arch pattern / decision | ❌ | ❌ | ✅ | ✅ | ❌ | ❌ |
| Config / env change | ✅ | ❌ | ❌ | optional | ❌ | ❌ |
| Refactor (no contract change) | ❌ | ❌ | ❌ | ❌ | ❌ | ❌ |
| Auth pattern changed | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ regenerate |
| Error handling changed | ❌ | ❌ | ❌ | optional | ❌ | ✅ regenerate |
| Build commands changed | ❌ | ❌ | ❌ | ❌ | ❌ | ✅ regenerate |
| Golden rules changed | ❌ | ❌ | ❌ | ✅ | ❌ | ✅ regenerate |

**exec-context.md regenerate rule:** run repo-scan or setup wizard to regenerate. Do not hand-edit.

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

Create `.ai/memory/{branch-slug}-{feature-slug}-context.md` for EPIC work.

**Naming rule:** `{branch-slug}` = current git branch name with `/` replaced by `-`.
Example: branch `feat/PROJ-42-auth` + feature `token-refresh` → `memory/feat-PROJ-42-auth-token-refresh-context.md`

This ensures no two developers on different branches collide on the same memory file.

**Expiry:** use `{{MEMORY_EXPIRY_DAYS}}` from `.ai/AGENTS.md` Team config (set by setup wizard). Default 14 if unset.

**On expiry (mandatory before delete):**

1. Append `## Decisions` entries → `docs/DECISIONS.md`
2. Append failure/lesson entries → `docs/LESSONS.md`
3. Write one-line summary → `docs/CHANGELOG.md`
4. Delete the memory file

```markdown
# <Feature> — Context
Branch: <git branch name>
Created-by: <first task ID>
Active-until: <last task ID> done
Owner: <module>
Auto-expire: <today + {{MEMORY_EXPIRY_DAYS}} days>

## State
## Decisions
## Lessons
## Tasks
| # | File | Status | Depends on |
| --- | --- | --- | --- |

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
