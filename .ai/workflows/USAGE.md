# AI Workflow — Usage Guide V9.3

## Setup: new project

```
Human: "Setup AI workflow for new project"
Claude: generates skeletons, SKILLS-TODO.md all ❓
→ Begin work → Claude fills SKILLS-TODO progressively
```

## Setup: existing project

```
Human: "Setup AI workflow for existing project"
Claude: runs repo-scan → pre-flight conflict check → human confirms
→ Merges/replaces files per strategy → SKILLS-TODO filled from scan
→ Begin work
```

## File merge strategy

| File/folder | On adopt |
|---|---|
| `workflows/*.md` | Replace |
| `routing.md` | Replace |
| `AGENTS.md` | Merge — keep project constraints |
| `skills/*.md` | Append only |
| `SKILLS-TODO.md` | Generate fresh |
| `tasks/**` | Never touch |
| `memory/**` | Never touch |
| `docs/**` | Never touch |
| `module-map.md` | Never touch |

## Everyday flow

```
Human writes requirement
  → Claude generates task files + execution plan
  → Paste Codex group → Codex panel
  → Paste Cline group → Cline terminal (if needed)
  → Review diff → use batch commit message → push
```

## Requirement template

```
MODULE: <name — see module-map.md>
TYPE: feature | bugfix | doc-debt | migration | epic
GOAL: <1–2 sentences>
ACCEPTANCE CRITERIA:
  - <condition 1>
  - <condition 2>
SCOPE CONSTRAINTS: <optional>
RELATED FILES: <optional>
```

## When Claude hits a ❓ in SKILLS-TODO.md

Claude stops, asks human once, fills the row, updates AGENTS.md, continues.

## Executor guide

| Need | Paste into |
|---|---|
| Code edits — any size | Codex |
| Codex quota exhausted | Cline (same task file) |
| Shell: migration, seed, build loop | Cline |

## Memory discipline

Check header before using a memory file:
```
Auto-expire: YYYY-MM-DD
Active-until: <task ID> done
```
When EPIC done: delete file + log in CHANGELOG.md.

## Optional scripts

```bash
pnpm validate-all     # batch EPIC sanity check
pnpm docs-sync        # pre-PR doc check
pnpm lint-workflows   # workflow drift check
```
