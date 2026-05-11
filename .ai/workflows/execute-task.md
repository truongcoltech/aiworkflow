# Workflow: Execute Task — V9.4

## Trigger

Human pastes task file path into Codex or Cline panel.

## Steps

1. Read task file: TASK, PATTERN, CONTEXT, GOAL, STEPS, DONE WHEN, DOC UPDATE, COMMIT.
2. Read PRIOR CONTEXT if present.
3. Read CONTEXT files listed. Grep for more if needed — no full repo scan.
4. Load applicable standards — **blocking step, not optional:**
   - Any code change → `.ai/standards/code-conventions.md`
   - New service / agent / endpoint / worker / bug fix → `.ai/standards/testing-policy.md`
   - Auth / billing / migration / tenant isolation / infra config / shared contracts → `.ai/standards/security.md`
   - Frontend component or page → `.ai/standards/ui-visual-testing.md`
   - Any STANDARD or EPIC task → `.ai/standards/definition-of-done.md`
5. If PATTERN set: read ARCHITECTURE.md for the relevant checklist.
6. Grep to confirm all file paths before editing.
7. Implement STEPS. Positive instructions only — constraints are in AGENTS.md.
8. Run smoke test if in DONE WHEN.
9. Run unit tests if in DONE WHEN.
10. Update docs per DOC UPDATE.
11. Validate against all loaded standards — **blocking gate before reporting done.**
    Hard gate failures block completion. Skipped soft gates must be documented in DONE WHEN with justification.
12. Check every DONE WHEN checkbox.
13. Report using AGENTS.md output format + COMMIT from task file.

If blocked: report `BLOCKED: <reason>`.

## Self-validation

- [ ] Files compile
- [ ] Only CONTEXT files modified
- [ ] Auth pattern followed (per AGENTS.md)
- [ ] All PATTERN checklist steps done or marked N/A
- [ ] All loaded standards hard gates passed — any failure blocks reporting done
- [ ] Soft gate skips documented with justification in DONE WHEN
- [ ] DOC UPDATE completed
