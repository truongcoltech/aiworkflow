# AGENTS.md — AI Workflow V9.3
# Project: <PROJECT_NAME>
# Stack: <filled by repo-scan or SKILLS-TODO.md>

## Language
<!-- Fill after repo-scan -->
Primary: <e.g. TypeScript / C# / Python>
Docs and comments: English only.

---

## Pre-coding read order (mandatory)

1. `AGENTS.md`
2. `docs/CUTOFF.md`
3. `.ai/skills/<module>.md` — if exists (skip source scan)
4. `docs/modules/<module>/*` — only if in CUTOFF.md AND no skill file
5. `.ai/memory/<feature-slug>-context.md` — only if resuming multi-session

Do not scan the entire repo. Load ARCHITECTURE.md only when task involves
a new resource, endpoint, or module (see PATTERN MATCHING in generate-tasks.md).

---

## Task-first rule

Check `.ai/tasks/` for a matching task before creating new work.
If found: execute or update. Do NOT create duplicates.

---

## Golden rules

1. Minimal change — no unsolicited refactors.
2. Grep before edit — confirm paths exist before touching files.
3. No hallucinated features — if unclear, stop and ask.
4. One final report — no intermediate dumps.
5. Stop on errors (compile fail, test fail, 4xx/5xx).
6. Self-validate against DONE WHEN before reporting done.
7. Update docs per DOC UPDATE before reporting done.
8. Task STEPS use positive instructions only.

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
