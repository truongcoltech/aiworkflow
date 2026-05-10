# Workflow: Repo Scan — V9.3
# Trigger: "Setup AI workflow for existing project"

## Phase 0 — Pre-flight conflict check

```bash
ls .ai/ 2>/dev/null && echo "EXISTS" || echo "FRESH"
```

**If FRESH:** skip to Phase 1.

**If EXISTS — output conflict report and wait for human confirmation:**

```
## Conflict report

Will REPLACE:
  .ai/workflows/*.md
  .ai/routing.md

Will MERGE (project content preserved):
  AGENTS.md  ← keep project constraints, update standard sections

Will GENERATE FRESH:
  .ai/SKILLS-TODO.md

Will NOT touch:
  .ai/tasks/**       ← N task files preserved
  .ai/memory/**      ← N memory files preserved
  .ai/skills/**      ← append only if new content
  docs/**            ← source of truth, never touched
  .ai/module-map.md  ← project-specific

Proceed? (confirm before any file is written)
```

---

## Phase 1 — Lightweight discovery

```bash
# Project type
find . -maxdepth 2 \
  -name "package.json" -o -name "*.csproj" -o \
  -name "pyproject.toml" -o -name "go.mod" -o \
  -name "Gemfile" -o -name "pubspec.yaml" \
  | grep -v node_modules | grep -v .git | sort

# App boundaries
ls apps/ packages/ services/ src/ themes/ 2>/dev/null

# Entry points
find . -maxdepth 4 \
  -name "main.ts" -o -name "index.ts" -o -name "Program.cs" -o \
  -name "main.py" -o -name "app.py" -o -name "server.ts" \
  | grep -v node_modules | grep -v dist | grep -v .git | head -20
```

Hard limit: read at most 5 files per app, max 80 lines each.
Never read: test files, dist/, node_modules/, .git/, bin/, obj/.

---

## Phase 2 — Stack detection → fill SKILLS-TODO.md

| Signal | Fills |
|---|---|
| `package.json` deps | Language, package manager |
| `*.csproj` | Language=C#, Runtime=ASP.NET |
| `next`, `react` | Frontend framework |
| `jest`, `vitest`, `xunit`, `pytest` | Test framework |
| `jsonwebtoken`, `JwtBearer`, `passport` | Auth=JWT |
| `prisma`, `typeorm`, `EntityFramework` | ORM |
| `tailwind` | CSS=Tailwind |

Mark ✅ detected, ❓ unknown.

---

## Phase 3 — Generate docs/CUTOFF.md skeleton

```markdown
# <Project> — Knowledge Cutoff
_Last updated: <today>_

## Documented modules
| Module | Doc location | Status |
|---|---|---|

## Undocumented modules
| Module | Location | Notes |
|---|---|---|
| <detected> | <path> | Auto-detected — no docs yet |

## Rule for agents
If undocumented: read source entry point directly.
```

---

## Phase 4 — Generate docs/ARCHITECTURE.md skeleton

```markdown
# <Project> — Architecture
_Status: auto-generated stub — needs review_

## Stack
- Language: <detected>
- Framework: <detected>
- Database: <detected>

## Apps / Services
### <app-name>
- Entry point: <path>
- Key deps: <from manifest>

## Runbook patterns
<!-- Fill during work -->
```

---

## Phase 5 — AGENTS.md merge

**If AGENTS.md exists (repo root):**
1. Read existing file
2. Keep project-specific sections verbatim (constraints, auth, build commands)
3. Replace standard sections (golden rules, output format, pre-coding read order)
4. Write merged result to repo root

**If AGENTS.md does not exist:** generate from template at repo root.

---

## Phase 6 — Output summary

```
## Repo scan complete

Stack detected:
  Language:   <value>
  Framework:  <value>
  Database:   <value>
  Auth:       <value>
  Tests:      <value>

Files written:
  ✓ AGENTS.md               (merged / generated — repo root)
  ✓ .ai/SKILLS-TODO.md      (N filled, N ❓)
  ✓ docs/CUTOFF.md          (skeleton)
  ✓ docs/ARCHITECTURE.md    (skeleton)

Not touched:
  — .ai/tasks/              (N files preserved)
  — .ai/memory/             (N files preserved)
  — docs/modules/           (existing docs preserved)

Action needed:
  [ ] Fill ❓ rows in .ai/SKILLS-TODO.md
  [ ] Review docs/ARCHITECTURE.md
  [ ] Fill .ai/module-map.md
  [ ] Add project constraints to AGENTS.md
```
