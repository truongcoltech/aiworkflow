# AGENTS.md — AI Workflow V9.3
# Project: <PROJECT_NAME>
# Stack: <filled by repo-scan or SKILLS-TODO.md>

## Language
<!-- Fill after repo-scan -->
Primary: <e.g. TypeScript / C# / Python>
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
4. `.ai/skills/<module>.md` — if exists (skip source scan)
5. `docs/modules/<module>/*` — only if in CUTOFF.md AND no skill file
6. `docs/ARCHITECTURE.md` — only if task involves new resource/endpoint/module
7. `.ai/memory/<feature-slug>-context.md` — only if resuming multi-session

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
7. Self-validate against DONE WHEN before reporting done.
8. Update docs only when the change affects how future humans or agents understand, navigate, or safely modify the system — use the doc trigger matrix in generate-tasks.md.
9. Task STEPS use positive instructions only.

---

## Authentication
<!-- Fill after repo-scan — describe auth pattern for this project -->
<!-- Example: JWT Bearer token / Session cookie / API key / OAuth -->
<AUTH_PATTERN>

---

## Error handling
<!-- Fill after repo-scan -->
<!-- Example patterns: -->
<!-- - API: structured { error, message, statusCode } + log full stack -->
<!-- - Workers: log { jobId, error, stack } on every failure -->
<!-- - UI: display error details in dev mode, not just toast -->
<ERROR_HANDLING_PATTERN>

---

## Testing
<!-- Fill after repo-scan -->
<!-- Example: -->
<!-- - New service method → unit test with mocks -->
<!-- - New API endpoint → smoke test (auth + happy path) -->
<!-- - Mock all external calls — never hit real DB or APIs in unit tests -->
<TESTING_PATTERN>

---

## Project-specific constraints
<!-- Fill during work — add rules that are NOT in golden rules above -->
<!-- Example: -->
<!-- - All DB queries must filter by tenantId -->
<!-- - Workers must not write DB directly -->
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

```
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
