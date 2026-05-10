# AI Routing — V9.3

## Flow

```
Human: "Setup AI workflow"
  → Claude: "New project or existing?"

  NEW PROJECT:
    → Generate skeletons with placeholders
    → SKILLS-TODO.md all ❓
    → Begin work → Claude fills SKILLS-TODO progressively

  EXISTING PROJECT:
    → repo-scan workflow
    → Pre-flight conflict check → human confirms
    → Merge/replace per strategy → SKILLS-TODO filled from scan
    → Begin work
```

## Everyday flow

```
Human writes requirement
  → Claude generates task files + execution plan
  → Human pastes Codex group → Codex panel
  → Human pastes Cline group → Cline terminal (if needed)
  → Human reviews diff → batch commit → push
```

## Roles

| Actor | Does | Reads |
|---|---|---|
| Claude | Planning, task generation, execution plan | AGENTS.md + CUTOFF.md + skills/ |
| Codex | Code edits — default executor | Task file + CONTEXT files only |
| Cline | Shell tasks or Codex quota fallback | Same task file, no edits needed |
| Haiku | Optional batch validate (pre-PR) | Diff + task + skills/ |

## Executor — human decides at paste time

Codex = all code edits, any size.
Cline = shell required OR Codex quota exhausted.
Execution plan groups by dependency and executor. Codex first, Cline last.

## Human gates

| Gate | When |
|---|---|
| Watch executor | Always — abort if scope drifts |
| Diff review | Before every commit |
| Task skim | Auth / schema tasks only |

## File merge strategy (existing projects)

| File/folder | On adopt |
|---|---|
| `AGENTS.md` | Merge — keep project constraints |
| `.ai/workflows/*.md` | Replace |
| `.ai/routing.md` | Replace |
| `.ai/SKILLS-TODO.md` | Generate fresh |
| `.ai/skills/*.md` | Append only |
| `.ai/tasks/**` | Never touch |
| `.ai/memory/**` | Never touch |
| `docs/**` | Never touch |
| `.ai/module-map.md` | Never touch |

## Optional scripts

```bash
pnpm validate-all     # batch EPIC sanity check
pnpm docs-sync        # pre-PR doc check
pnpm lint-workflows   # workflow drift check
```

## Doc paths
<!-- Update to match your project after setup -->
| What | Path |
|---|---|
| Module registry | `docs/CUTOFF.md` |
| Architecture | `docs/ARCHITECTURE.md` |
| Decisions | `docs/DECISIONS.md` |
| Changelog | `docs/CHANGELOG.md` |
| Skills | `.ai/skills/<module>.md` |
