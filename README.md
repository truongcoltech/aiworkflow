# AI Workflow — V9.3

Developer guide for this project's AI-assisted development workflow.

## Structure

```
AGENTS.md          ← rules for all AI agents (repo root — auto-loaded)
.ai/
  SKILLS-TODO.md   ← tech stack registry, fill during work
  module-map.md    ← module name mappings
  routing.md       ← flow, roles, executor guide
  workflows/       ← agent runbooks (generate-tasks, feature, bugfix...)
  skills/          ← module interface summaries
  tasks/           ← execution task files, grouped by module
  memory/          ← multi-session context snapshots (EPICs)
docs/
  CUTOFF.md        ← module registry (what's documented vs not)
  ARCHITECTURE.md  ← system architecture
  DECISIONS.md     ← key architecture decisions + rationale
  CHANGELOG.md     ← completed EPICs log
```

---

## Setup

**New project:**
```
Tell Claude: "Setup AI workflow for new project"
→ Claude generates skeletons, SKILLS-TODO.md all ❓
→ Begin work — Claude fills stack info progressively
```

**Existing project:**
```
Tell Claude: "Setup AI workflow for existing project"
→ Claude runs repo-scan → conflict check → you confirm
→ Claude merges/replaces files, fills SKILLS-TODO from scan
→ Begin work
```

---

## Daily flow

```
1. Write requirement → Claude generates task files + execution plan
2. Paste Codex group → Codex panel   (code edits)
3. Paste Cline group → Cline terminal (shell tasks, if any)
4. Review diff → commit using batch commit message → push
```

---

## Requirement template

```
MODULE: <name — see .ai/module-map.md>
TYPE: feature | bugfix | doc-debt | migration | epic
GOAL: <1–2 sentences>
ACCEPTANCE CRITERIA:
  - <condition 1>
  - <condition 2>
SCOPE CONSTRAINTS: <optional>
RELATED FILES: <optional>
```

---

## Executor guide

| Need | Paste into |
|---|---|
| Code edits — any size | Codex |
| Codex quota exhausted | Cline (same task file, no edits) |
| Shell: migration, seed, build loop | Cline |

Codex groups always first. Cline (shell) tasks always last.

---

## Commit format

```
type(scope): subject

- what changed and why
- key invariant enforced

Breaking: none | <what breaks>
Migration: none | <migration needed>
```

Types: `feat` | `fix` | `refactor` | `test` | `docs` | `chore`

---

## File merge strategy (when adopting into existing project)

| File/folder | On adopt |
|---|---|
| `AGENTS.md` | Merge — keep project constraints |
| `.ai/workflows/*.md` | Replace |
| `.ai/routing.md` | Replace |
| `.ai/SKILLS-TODO.md` | Generate fresh |
| `.ai/skills/*.md` | Append only — never overwrite |
| `.ai/tasks/**` | Never touch |
| `.ai/memory/**` | Never touch |
| `docs/**` | Never touch |
| `.ai/module-map.md` | Never touch |

---

## When Claude hits a ❓ in SKILLS-TODO.md

Claude stops, asks you once, fills the row, updates AGENTS.md, continues.

---

## Memory files

Check header before Claude uses one:
```
Auto-expire: YYYY-MM-DD
Active-until: <task ID> done
```
When EPIC done: delete file + log in `docs/CHANGELOG.md`.

---

## Optional scripts

```bash
pnpm validate-all     # batch EPIC sanity check
pnpm docs-sync        # pre-PR doc check
pnpm lint-workflows   # workflow drift check
```
