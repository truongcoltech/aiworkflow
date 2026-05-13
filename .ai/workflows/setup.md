# Setup Wizard — AI Workflow V1.0

## Trigger

Run this workflow when human says any of:
`"setup ai workflow"` · `"setup workflow"` · `"init workflow"` · `"initialize workflow"`

---

## Behavior rules

- Ask **one question at a time**. Wait for answer before moving to the next.
- Do **not** write any files mid-interview — collect all answers first.
- After all questions answered: output the complete filled file list and write them.
- If human skips a question: use the listed default and note it.
- If answer is ambiguous: ask one clarifying follow-up, then proceed.

---

## Interview

### Q1 — Project type
Ask:
> "Is this a **new project** (empty repo) or an **existing codebase**?"

Options: `new` / `existing`

Record as: `{{PROJECT_TYPE}}`

- If `existing`: repo-scan runs after setup to fill SKILLS-TODO.md. Remind human.
- If `new`: skeleton generation runs after setup.

---

### Q2 — Project name
Ask:
> "What is the **project name**? (used as slug in task files and commit scopes — lowercase, kebab-case)"

Record as: `{{PROJECT_NAME}}`

---

### Q3 — Team size
Ask:
> "What is the **team size**?"

Options:
- `solo` — 1 developer
- `small` — 2–5 developers
- `medium` — 6–20 developers
- `large` — 20+ developers

Record as: `{{TEAM_SIZE}}`

Drives: tasks/ naming strictness, PR reviewer requirements, memory expiry, setup question scope.

**If `solo`: skip Q5, Q9, Q10 — auto-derive defaults below and continue to Q4.**

| Auto-derived for solo | Value |
| --- | --- |
| Q5 — Git platform | `GitHub` |
| Q9 — Ticket format | `none` |
| Q10 — Workflow owner | `developer` |

---

### Q4 — Primary language and framework
Ask:
> "What is the **primary language and main framework**?
> (e.g. TypeScript + Next.js · Python + FastAPI · Go + Gin · Java + Spring Boot)"

Record language as: `{{PRIMARY_LANGUAGE}}`
Record framework as: `{{FRAMEWORK}}`

Pre-fills: SKILLS-TODO.md Language + Runtime rows → mark ✅.

---

### Q5 — Git platform
Ask:
> "Which **git platform** does the team use?"

Options: `GitHub` / `GitLab` / `Azure DevOps` / `Bitbucket` / `other`

Record as: `{{GIT_PLATFORM}}`

Drives: PR template format, branch convention details.

---

### Q6 — Git workflow
Ask:
> "Which **git workflow** does the team follow?"

Options:
- `PR-based` — feature branches → PR → merge to main (most common)
- `trunk-based` — short-lived branches, frequent integration to main
- `gitflow` — main + develop + feature/release/hotfix branches

Record as: `{{GIT_FLOW}}`

Drives: tasks/ path convention, branch naming strictness.

---

### Q7 — AI tools in use
Ask:
> "Which **AI tools** does the team use? (list all that apply)"

Options: `Claude Code` · `Cursor` · `GitHub Copilot` · `Codex` · `Cline` · `other`

Record as: `{{AI_TOOLS}}`

Drives: routing.md executor section. If only Claude Code: simplify executor routing.

---

### Q8 — Model cost preference
Ask:
> "What is the **model cost preference** for this project?"

Options:
- `cost-first` — maximize Haiku usage, lowest API cost
- `balanced` — Haiku for light tasks, Sonnet for complex (recommended)
- `quality-first` — Sonnet as default, Opus for architecture decisions

Default if skipped: `balanced`

Record as: `{{MODEL_BUDGET}}`

Drives: model routing table in AGENTS.md and routing.md.

---

### Q9 — Ticket system
Ask:
> "Does the team use a **ticket/issue tracker**? If yes, what prefix format?
> (e.g. `PROJ-123` for Jira · `#123` for GitHub Issues · `none`)"

Record as: `{{TICKET_FORMAT}}` (or `none`)

Drives: branch naming format.

---

### Q10 — Workflow owner
Ask:
> "Who **owns this AI workflow config**? (who updates AGENTS.md, approves rule changes)"

Options:
- `tech-lead` — one designated person owns it
- `team` — anyone can propose changes via PR

Record as: `{{WORKFLOW_OWNER}}`

---

## Derived values (compute after interview — do not ask human)

### tasks/ naming convention → `{{TASKS_PATH_CONVENTION}}`

| `{{GIT_FLOW}}` | Convention |
| --- | --- |
| `PR-based` or `gitflow` | `tasks/{branch-slug}/{NNN-name}.md` |
| `trunk-based` | `tasks/{author}/{NNN-name}.md` |
| `solo` team size | `tasks/{NNN-name}.md` (flat — no subdirectory needed) |

### Branch naming format → `{{BRANCH_FORMAT}}`

```text
feat/{ticket?}-{slug}      new feature
fix/{ticket?}-{slug}       bug fix
chore/{slug}               tooling, deps, config
refactor/{slug}            code restructure, no behavior change
docs/{slug}                documentation only
test/{slug}                tests only
```

If `{{TICKET_FORMAT}}` is `none`: omit ticket segment → `feat/{slug}`.
If `{{TICKET_FORMAT}}` is set: `feat/PROJ-123-{slug}` or `feat/#123-{slug}`.

### PR reviewer count → `{{PR_REVIEWERS}}`

| `{{TEAM_SIZE}}` | Required reviewers |
| --- | --- |
| `solo` | 0 (self-merge allowed) |
| `small` | 1 |
| `medium` | 1–2 |
| `large` | 2 |

### EPIC memory expiry → `{{MEMORY_EXPIRY_DAYS}}`

| `{{TEAM_SIZE}}` | Days | Rationale |
| --- | --- | --- |
| `solo` | 60 | Long-running projects, infrequent context switches |
| `small` | 30 | Moderate cadence, some parallel EPICs |
| `medium` | 21 | Higher PR throughput, faster EPIC cycles |
| `large` | 14 | Many concurrent EPICs, stale context risk rises quickly |

**Expiry action (mandatory):** when a memory file reaches expiry date, before deleting:

1. Extract all `## Decisions` entries → append to `docs/DECISIONS.md`
2. Extract any failure/lesson entries → append to `docs/LESSONS.md`
3. Write a one-line summary to `docs/CHANGELOG.md`
4. Then delete the memory file

### Model routing table → `{{MODEL_ROUTING}}`

**cost-first:**
| Triage | Model | Notes |
| --- | --- | --- |
| TRIVIAL | `claude-haiku-4-5-20251001` | Direct edits, single file |
| SIMPLE | `claude-haiku-4-5-20251001` | 2-section task note |
| STANDARD | `claude-sonnet-4-6` | Cross-module, design decision |
| EPIC | `claude-sonnet-4-6` | Multi-session, new architecture |
| Batch validate (pre-PR) | `claude-haiku-4-5-20251001` | Diff + task review |

**balanced (default):**
| Triage | Model | Notes |
| --- | --- | --- |
| TRIVIAL | `claude-haiku-4-5-20251001` | Direct edits, single file |
| SIMPLE | `claude-haiku-4-5-20251001` | 2-section task note |
| STANDARD | `claude-sonnet-4-6` | Cross-module, design decision |
| EPIC | `claude-sonnet-4-6` · `claude-opus-4-7` (pure arch decisions only) | Multi-session |
| Batch validate (pre-PR) | `claude-haiku-4-5-20251001` | Diff + task review |

**quality-first:**
| Triage | Model | Notes |
| --- | --- | --- |
| TRIVIAL | `claude-haiku-4-5-20251001` | Direct edits, single file |
| SIMPLE | `claude-sonnet-4-6` | 2-section task note |
| STANDARD | `claude-sonnet-4-6` | Cross-module, design decision |
| EPIC | `claude-opus-4-7` | Multi-session, new architecture |
| Batch validate (pre-PR) | `claude-sonnet-4-6` | Diff + task review |

---

## Output — files to write after interview

Write all files in this order. Replace every `{{variable}}` with the recorded value.
Do not leave any `{{variable}}` token in the output.

### 1. `AGENTS.md`

Fill these tokens:
- `{{PROJECT_NAME}}`
- `{{PRIMARY_LANGUAGE}}`
- `{{MODEL_ROUTING}}` — paste the correct table from MODEL_BUDGET
- `{{BRANCH_FORMAT}}`
- `{{TASKS_PATH_CONVENTION}}`
- `{{PR_REVIEWERS}}`
- `{{GIT_PLATFORM}}`
- `{{WORKFLOW_OWNER}}`
- `{{TICKET_FORMAT}}`
- `{{MEMORY_EXPIRY_DAYS}}` — from EPIC memory expiry derived value

Leave these for repo-scan or first work session (mark clearly as `[fill after repo-scan]`):
- `{{AUTH_PATTERN}}`
- `{{ERROR_HANDLING_PATTERN}}`
- `{{TESTING_PATTERN}}`
- `{{PROJECT_CONSTRAINTS}}`
- Build commands

### 2. `.ai/routing.md`

Update executor section: replace Codex/Cline references with `{{AI_TOOLS}}` list.
Fill model routing table with the correct `{{MODEL_ROUTING}}` table.

### 3. `.ai/SKILLS-TODO.md`

Pre-fill rows from interview answers:
- Language → `{{PRIMARY_LANGUAGE}}` → ✅
- Runtime / framework → `{{FRAMEWORK}}` → ✅
- Leave other rows as ❓

### 4. `docs/ARCHITECTURE.md`

Fill header:
- Project: `{{PROJECT_NAME}}`
- Stack: `{{PRIMARY_LANGUAGE}} + {{FRAMEWORK}}`
- Leave all other sections as template placeholders for first work session.

---

## Completion checklist

After writing all files, output this checklist to human:

```
Setup complete — {{PROJECT_NAME}}

✅ AGENTS.md filled (model routing, branch conventions, team config)
✅ routing.md updated (executor tools, model routing table)
✅ SKILLS-TODO.md pre-filled (language + framework)
✅ ARCHITECTURE.md header filled

Remaining — fill during first work session or repo-scan:
[ ] Auth pattern
[ ] Error handling pattern
[ ] Testing commands
[ ] Project-specific constraints
[ ] Build / lint / test commands
[ ] Module skill files (.ai/skills/)
[ ] Module map (.ai/module-map.md)

Next step:
```
```
→ NEW PROJECT: "Generate project skeleton"
→ EXISTING PROJECT: "Run repo-scan"
```
```

---

## Hard rules for setup

- Never guess a `{{variable}}` value — only use what human answered.
- Never write files until all 10 questions are answered.
- Never skip the completion checklist output.
- If human wants to change an answer after files are written: re-run setup from Q1 or surgically update the specific token and re-output the affected file only.
