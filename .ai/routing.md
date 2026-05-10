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
    → Pre-flight conflict check (see below)
    → Human confirms → merge/replace per strategy
    → SKILLS-TODO.md auto-filled from scan
    → Begin work
```

## Everyday flow (after setup)

```
Human writes requirement
  → Claude generates task files + execution plan
  → Human pastes Codex group → Codex panel
  → Human pastes Cline group → Cline terminal
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
Cline = shell required (migration, seed, build loop) OR Codex quota exhausted.
Execution plan groups tasks by dependency and executor. Codex first, Cline last.

## Human gates

| Gate | When |
|---|---|
| Watch executor | Always — abort if scope drifts |
| Diff review | Before every commit |
| Task skim | Auth / schema tasks only |

## File merge strategy (existing projects)

| File/folder | On adopt |
|---|---|
| `workflows/*.md` | **Replace** — standard files, no project content |
| `routing.md` | **Replace** — update doc paths section only |
| `AGENTS.md` | **Merge** — keep project constraints, replace standard sections |
| `skills/*.md` | **Append only** — never overwrite existing content |
| `SKILLS-TODO.md` | **Generate fresh** — repo-scan fills it |
| `tasks/**` | **Never touch** |
| `memory/**` | **Never touch** |
| `docs/**` | **Never touch** |
| `module-map.md` | **Never touch** |

## Optional scripts

```bash
pnpm validate-all     # batch EPIC sanity check
pnpm docs-sync        # pre-PR doc check
pnpm lint-workflows   # workflow drift check
```

## Doc paths
<!-- Update these to match your project after setup -->
| What | Path |
|---|---|
| Module registry | `docs/CUTOFF.md` |
| Architecture | `docs/ARCHITECTURE.md` |
| Decisions | `docs/DECISIONS.md` |
| Changelog | `docs/CHANGELOG.md` |
| Skills | `.ai/skills/<module>.md` |
