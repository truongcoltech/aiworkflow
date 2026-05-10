# Workflow: Execute Task — V9.3

## Trigger
Human pastes task file path into Codex or Cline panel.

## Steps
1. Read task file: TASK, PATTERN, CONTEXT, GOAL, STEPS, DONE WHEN, DOC UPDATE, COMMIT.
2. Read PRIOR CONTEXT if present.
3. Read CONTEXT files listed. Grep for more if needed — no full repo scan.
4. If PATTERN set: read ARCHITECTURE.md for the relevant checklist.
5. Grep to confirm all file paths before editing.
6. Implement STEPS. Positive instructions only — constraints are in AGENTS.md.
7. Run smoke test if in DONE WHEN.
8. Run unit tests if in DONE WHEN.
9. Update docs per DOC UPDATE.
10. Check every DONE WHEN checkbox.
11. Report using AGENTS.md output format + COMMIT from task file.

If blocked: report `BLOCKED: <reason>`.

## Self-validation
- Files compile
- Only CONTEXT files modified
- Auth pattern followed (per AGENTS.md)
- All PATTERN checklist steps done or marked N/A
- DOC UPDATE completed
