# AGENTS.md — AI Workflow V9.4

Project: `<PROJECT_NAME>`
Stack: `<fill after repo-scan — see SKILLS-TODO.md>`

## Language

Primary: <e.g. TypeScript / Python / Go>
Docs and comments: English only.

---

## Triage (mandatory — before any context load)

Classify every incoming change before loading context or generating tasks.
See `.ai/workflows/generate-tasks.md` TRIAGE section for full rules.

| Level | Criteria | Output |
|---|---|---|
| TRIVIAL | Single file, no public contract change, clear validation, low blast radius | Direct fix — no task file |
| SIMPLE | ≤2 files, no cross-module contract, clear path, low blast radius | Task note: TASK + DONE WHEN only |
| STANDARD | Cross-file, design decision, public contract changed, or validation unclear | Full task file |
| EPIC | Multi-session, multiple modules, new architecture pattern | Full task file + memory file |

**Safety override — force STANDARD regardless of above when touching:**
auth / session / tokens · payment / billing · database migrations · tenant isolation · infra / runtime config · shared contracts (types exported across modules)

---

## Pre-coding read order (load per classification — not always all steps)

**TRIVIAL:** AGENTS.md golden rules + grep target file only.

**SIMPLE:** AGENTS.md + `.ai/skills/<module>.md` only.

**STANDARD / EPIC:**

1. `AGENTS.md`
2. `docs/CUTOFF.md`
3. `.ai/SKILLS-TODO.md` — check ❓ rows before starting
4. `.ai/skills/{module}.md` — if exists (skip source scan)
5. `docs/modules/{module}/` — only if in CUTOFF.md AND no skill file
6. `docs/ARCHITECTURE.md` — only if task involves new resource/endpoint/module
7. `docs/LESSONS.md` — if task touches a module with a known lesson entry
8. `.ai/memory/{feature-slug}-context.md` — only if resuming multi-session

Do not scan the entire repo.

---

## Task-first rule

Check `.ai/tasks/` for a matching task before creating new work.
If found: execute or update. Do NOT create duplicates.

---

## Golden rules

1. Triage before context load — classify first, load only what the level requires.
2. Minimal change — no unsolicited refactors.
3. Grep before edit — confirm paths exist before touching files.
4. No hallucinated features — if unclear, stop and ask.
5. One final report — no intermediate dumps.
6. Stop on errors (compile fail, test fail, 4xx/5xx).
7. Done = DONE WHEN conditions met AND applicable standards hard gates pass. Both are required.
8. Update docs only when the change affects how future humans or agents understand, navigate, or safely modify the system — use the doc trigger matrix in `generate-tasks.md`.
9. Task STEPS use positive instructions only.

---

## Standards

Load relevant standards before implementation. Match to task type — not all for every task.

| Task touches | Load |
|---|---|
| Any code change | `.ai/standards/code-conventions.md` |
| Auth, billing, migrations, tenant isolation, infra config, shared contracts | `.ai/standards/security.md` |
| New service / endpoint / worker / bug fix | `.ai/standards/testing-policy.md` |
| Frontend component or page | `.ai/standards/ui-visual-testing.md` |
| Any STANDARD or EPIC task | `.ai/standards/definition-of-done.md` |

Validate against loaded standards before reporting done. Standards are part of DONE WHEN.

---

## Authentication

<!-- Fill after repo-scan -->
<!-- Examples: JWT Bearer token · Session cookie · API key · OAuth 2.0 -->

Auth pattern: <AUTH_PATTERN>

Key questions to fill in:

- How are tokens verified? (middleware vs handler)
- Where does tenant/org/workspace scoping come from? (session vs request body)
- What is the role/permission model?

---

## Error handling

<!-- Fill after repo-scan -->
<!-- Examples: -->
<!-- - API: return { error, message, statusCode } + log full stack server-side -->
<!-- - Background jobs: log { jobId, error, stack } on every failure, never swallow -->
<!-- - UI: show error details in dev mode only, user-friendly message in production -->

<ERROR_HANDLING_PATTERN>

---

## Testing

Full test policy: see `.ai/standards/testing-policy.md`

<!-- Fill after repo-scan — add project-specific test commands and key rules -->
<!-- Examples: -->
<!-- - Unit tests: mock all external calls — never hit real DB or API -->
<!-- - Integration tests: use real test DB — never mock the database -->
<!-- - Bug fixes: always include a regression test -->

<TESTING_PATTERN>

---

## Project-specific constraints

<!-- Fill during work — rules specific to this project not covered by golden rules or standards -->
<!-- Examples: -->
<!-- - All DB queries must filter by tenantId -->
<!-- - No business logic in worker/job files — dispatch only -->
<!-- - Never overwrite versioned records without creating a snapshot first -->

<PROJECT_CONSTRAINTS>

---

## Build commands

<!-- Fill after repo-scan -->

| Command | What |
|---|---|
| `<build>` | Build project |
| `<typecheck>` | Type check only |
| `<test>` | Run tests |
| `<lint>` | Lint |

---

## Output format (mandatory)

```text
Files changed:
- path — summary

Docs updated:
- path — what changed   (or: none required)

Commit message:
type(scope): subject

- what changed and why
- key invariant enforced (if any)

Breaking: none | <what breaks>
Migration: none | <migration needed>

DONE WHEN verified: ✓ all conditions met
```

Commit types: `feat` | `fix` | `refactor` | `test` | `docs` | `chore`
