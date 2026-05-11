# Workflow: Bugfix — V9.3

## Steps
1. Read AGENTS.md and relevant skill/module docs.
2. Identify exact failing path — error, status code, stack trace.
3. Grep to find root cause. No assumptions.
4. Check DECISIONS.md — is a known constraint being violated?
5. Generate scoped fix task using generate-tasks.md.
   DONE WHEN: bug no longer reproduces + smoke test passes.
6. Output execution plan (usually single Codex task).
7. Human executes. Minimal fix — abort if scope expands.
8. Commit with root cause in body.

## Rules
- Touch only the failing path.
- If bug = missing auth/guard → add rule to AGENTS.md + log in DECISIONS.md.
- If same pattern appears twice → add as golden rule in AGENTS.md.
- If fix reveals a pattern that must never repeat → add a LESSON entry to `docs/LESSONS.md` (see format there). Not every bug — only dangerous patterns.
