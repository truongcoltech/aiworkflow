# AI Routing — V1.0

## Flow

```text
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

```text
Human writes requirement
  → Claude triages (TRIVIAL / SIMPLE / STANDARD / EPIC)

  TRIVIAL:
    → Direct fix (no task file)
    → Human reviews diff → commit → push

  SIMPLE:
    → Task note (TASK + DONE WHEN only)
    → Human runs task → executor (see {{AI_TOOLS}})
    → Human reviews diff → commit → push

  STANDARD / EPIC:
    → Claude generates task files + execution plan
    → Human runs code-edit group → executor
    → Human runs shell group → terminal (if needed)
    → Human reviews diff → batch commit → push
```

## Roles

| Actor | Does | Reads |
| --- | --- | --- |
| Claude | Triage → planning, task generation (STANDARD/EPIC), execution plan | AGENTS.md + CUTOFF.md + skills/ + standards/ |
| Executor | Code edits — tool(s) listed in `{{AI_TOOLS}}` | Task file + CONTEXT files only |
| Shell runner | Shell tasks — Cline or terminal | Same task file, no edits needed |
| Haiku | Optional batch validate (pre-PR) | Diff + task + skills/ |
| Human | Gate: watch scope, diff review, auth/schema task skim, PR approve | — |

<!-- {{AI_TOOLS}} is filled by setup wizard. If only Claude Code: executor = Claude Code directly. -->

## Model routing

<!-- Filled by setup wizard. Selected table based on {{MODEL_BUDGET}}. -->

`{{MODEL_ROUTING}}`

**Rule:** Never upgrade model without updating this table. Never use Opus for TRIVIAL or SIMPLE tasks.

## Executor — human decides at run time

| Triage level | What happens |
| --- | --- |
| TRIVIAL | Claude implements directly — no task file |
| SIMPLE | 2-section task note → executor |
| STANDARD / EPIC | Full task file + execution plan → code edits first, shell tasks last |

## Human gates

| Gate | When |
| --- | --- |
| Watch executor | Always — abort if scope drifts |
| Diff review | Before every commit |
| Task skim | Auth / schema tasks only |

## File merge strategy (existing projects)

| File/folder | On adopt |
| --- | --- |
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
| --- | --- |
| Module registry | `docs/CUTOFF.md` |
| Architecture | `docs/ARCHITECTURE.md` |
| Decisions | `docs/DECISIONS.md` |
| Changelog | `docs/CHANGELOG.md` |
| Skills | `.ai/skills/{module}.md` |
