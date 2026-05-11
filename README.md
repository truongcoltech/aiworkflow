# AI Workflow — V9.4

A generic, project-agnostic AI-assisted development workflow. Covers triage, task generation, execution, standards enforcement, and doc discipline. Works with any language or stack.

---

## How to adopt

Two paths — pick one based on whether a codebase already exists.

| Situation | Use |
|---|---|
| Starting a new project from scratch | [New project template](#new-project-template) |
| Adding the workflow to an existing codebase | [Existing project template](#existing-project-template) |

Copy the relevant template below, fill in your values, and paste the whole thing into Claude chat.

---

## New project template

Copy this block, fill in every field, paste into Claude chat.

```text
Setup AI workflow for new project.

───────────────────────────────────────────────────
 STACK
───────────────────────────────────────────────────

Project name: MyApp
  # Your project name — used in doc titles and commit scope

Language: TypeScript
  # Options: TypeScript / JavaScript / Python / Go / C# / Java / PHP / Ruby / Rust

Runtime / framework: Express.js
  # Options: Express.js / Fastify / NestJS / FastAPI / Django / Flask / Rails / Spring Boot / Laravel / None

Frontend framework: Next.js 14
  # Options: Next.js / React / Vue / Svelte / Angular / None

CSS approach: Tailwind
  # Options: Tailwind / CSS Modules / Styled Components / SCSS / None

Database: PostgreSQL
  # Options: PostgreSQL / MySQL / SQLite / MongoDB / DynamoDB / Redis / None

ORM / query builder: Prisma
  # Options: Prisma / TypeORM / Knex / Drizzle / Sequelize / SQLAlchemy / GORM / ActiveRecord / None

Package manager: npm
  # Options: npm / pnpm / yarn / pip + venv / cargo / go mod / composer / bundler

Build tool: tsc + Vite
  # Options: tsc / Vite / Webpack / esbuild / Turbopack / Parcel / Rollup / None

Test framework: Jest + Supertest
  # Options: Jest / Vitest / Mocha / pytest / go test / NUnit / RSpec / PHPUnit

Auth pattern: JWT Bearer token
  # Options: JWT Bearer / Session cookie / API key in header / OAuth 2.0 / SAML / None

Deployment target: Railway
  # Options: Vercel / Railway / Fly.io / Heroku / Render / AWS ECS / Docker + Kubernetes / VPS

───────────────────────────────────────────────────
 BUILD COMMANDS  (replace with your actual commands)
───────────────────────────────────────────────────

Build:       npm run build
  # Command to compile or bundle the project

Type check:  tsc --noEmit
  # Command for type checking only (no output). e.g. mypy src/ / go vet ./...

Test:        npm test
  # Command to run all tests. e.g. pytest / go test ./... / dotnet test

Lint:        npm run lint
  # Command to run linter. e.g. flake8 / golangci-lint run / rubocop

Dev server:  npm run dev
  # Command to start local development server. e.g. uvicorn main:app --reload

───────────────────────────────────────────────────
 AUTH AND ROLE MODEL  (manual — describe your scheme)
───────────────────────────────────────────────────

JWT Bearer token verified in Express middleware (not inside route handlers).
Roles: admin, editor, viewer.
All DB queries filter by tenantId from the authenticated session token.
Role checks are explicit per endpoint — "authenticated" does not imply "authorized."

  # Replace the above with your actual auth scheme and role model.
  # Examples:
  #   Session-based: "Session cookie set on login, userId in session. No roles — single-user app."
  #   API key:       "X-API-Key header verified in middleware. Keys scoped to a workspace."
  #   OAuth:         "Google OAuth 2.0 via NextAuth. Roles stored in DB: owner / member."

───────────────────────────────────────────────────
 ERROR HANDLING CONVENTIONS  (manual — describe per context)
───────────────────────────────────────────────────

API:             Return { error, message, statusCode }. Log full stack server-side. Never expose trace to client.
Background jobs: Log { jobId, error, stack } on every failure. Never swallow errors.
UI:              Show full error details in dev mode. User-friendly message only in production.

  # Replace with your actual conventions.
  # Examples:
  #   "API errors use RFC 7807 Problem Details format."
  #   "Worker failures are written to a dead-letter queue, not just logged."
  #   "UI uses a global error boundary + toast notification."

───────────────────────────────────────────────────
 PROJECT-SPECIFIC CONSTRAINTS  (manual — rules agents must follow)
───────────────────────────────────────────────────

- All DB queries touching user data must filter by tenantId.
- No business logic in route handlers — service layer only.
- Never overwrite versioned records without creating a snapshot first.

  # Replace with rules specific to your codebase.
  # These become golden rules agents must follow during every task.
  # Examples:
  #   "All writes must go through the domain service, not the repo directly."
  #   "Do not add npm packages without noting them in the task COMMIT block."
  #   "All public API responses must match the OpenAPI spec."
  # Leave blank if none yet — add during work as rules are discovered.
```

---

## Existing project template

For existing codebases, Claude auto-detects the stack via repo-scan. You only need to fill in the parts Claude cannot infer from code.

```text
Setup AI workflow for existing project.

───────────────────────────────────────────────────
 PROJECT NAME  (manual)
───────────────────────────────────────────────────

Project name: MyApp
  # Your project name

───────────────────────────────────────────────────
 AUTH AND ROLE MODEL  (manual — cannot be detected from code)
───────────────────────────────────────────────────

JWT Bearer token verified in Express middleware.
Roles: admin, editor, viewer.
All DB queries filter by tenantId from the session token.

  # Replace with your actual scheme. See new project template for examples.

───────────────────────────────────────────────────
 ERROR HANDLING CONVENTIONS  (manual)
───────────────────────────────────────────────────

API:             Return { error, message, statusCode }. Log full stack server-side.
Background jobs: Log { jobId, error, stack } on failure. Never swallow.
UI:              Full details in dev mode, friendly message in production.

  # Replace with your conventions. See new project template for examples.

───────────────────────────────────────────────────
 PROJECT-SPECIFIC CONSTRAINTS  (manual)
───────────────────────────────────────────────────

- All DB queries must filter by tenantId.
- No business logic in route handlers — service layer only.

  # Replace with your rules, or leave blank and add during work.

───────────────────────────────────────────────────
 NOTE
───────────────────────────────────────────────────

Stack, framework, build commands, package manager, test runner, and
module structure will be auto-detected from the repo. After setup,
review SKILLS-TODO.md and correct any rows Claude got wrong.
```

---

## What Claude does with these templates

| Field | What Claude does |
|---|---|
| Project name | Fills `AGENTS.md` header, doc titles (ARCHITECTURE.md, CUTOFF.md, etc.) |
| Stack fields | Fills `SKILLS-TODO.md` rows, `AGENTS.md` stack line, `ARCHITECTURE.md` stack section |
| Build commands | Fills `AGENTS.md` build commands table |
| Auth and role model | Fills `AGENTS.md` Authentication section |
| Error handling conventions | Fills `AGENTS.md` Error handling section |
| Project-specific constraints | Fills `AGENTS.md` Project-specific constraints section |
| *(existing project only)* repo-scan | Auto-fills SKILLS-TODO.md, detects module structure, fills CUTOFF.md |

---

## Placeholder reference

Fields that require ongoing attention during development (not just setup).

### AGENTS.md — fill during work

| Placeholder | When to fill |
|---|---|
| `<TESTING_PATTERN>` | After first test is written — add project test commands and rules not already in `testing-policy.md` |
| `<PROJECT_CONSTRAINTS>` | Add rules as they are discovered during work. One rule per line. |
| Build commands (if repo-scanned wrong) | Correct after setup if auto-detection was inaccurate |

### docs/ARCHITECTURE.md — fill progressively

| Section | When to fill |
|---|---|
| App/service names and purposes | During first EPIC or system overview session |
| Key flows | Add one flow each time a major flow is built |
| Runbook patterns | Add the first time a pattern is used (new endpoint, new resource, etc.) |

### docs/CUTOFF.md — update after each EPIC

Add a row to the documented modules table each time a module gets a skill file or full docs.

### docs/DECISIONS.md — fill when a non-obvious rule is added

One `DECISION-NNN` block per constraint added to `AGENTS.md` that is not self-explanatory.

### docs/CHANGELOG.md — fill after each completed EPIC

One entry per EPIC. Delete the corresponding memory file from `.ai/memory/` after logging.

### .ai/module-map.md — fill during repo-scan or first work session

Add one mapping per module: `<human phrase> -> <kebab-case-module-name>`

### .ai/standards/ — fill stack-specific sections after repo-scan

| File | What to fill |
|---|---|
| `code-conventions.md` | Naming conventions per language, ORM rules, framework-specific anti-patterns |
| `security.md` | Your project's ownership/scoping key (e.g. `tenantId`), sensitive field encryption requirements |
| `testing-policy.md` | Project-specific rows in the test matrix, test runner commands |

---

## File merge strategy (existing projects)

| File / folder | On adopt |
|---|---|
| `AGENTS.md` | Merge — keep existing project constraints |
| `.ai/workflows/*.md` | Replace |
| `.ai/routing.md` | Replace |
| `.ai/SKILLS-TODO.md` | Generate fresh from repo-scan |
| `.ai/skills/*.md` | Append only — never overwrite |
| `.ai/tasks/**` | Never touch |
| `.ai/memory/**` | Never touch |
| `docs/**` | Never touch |
| `.ai/module-map.md` | Never touch |

---

## Repository structure

```text
AGENTS.md                   ← rules for all AI agents (filled per project)
README.md                   ← this file — setup guide and templates
.ai/
  SKILLS-TODO.md            ← tech stack registry (❓ rows filled during work)
  module-map.md             ← phrase → module name mappings
  routing.md                ← flow, roles, executor guide
  workflows/
    generate-tasks.md       ← how Claude generates task files
    execute-task.md         ← how Codex/Cline executes a task
    feature.md              ← feature workflow steps
    bugfix.md               ← bugfix workflow steps
  standards/
    code-conventions.md     ← naming, structure, error handling, logging
    security.md             ← isolation, auth, secrets, audit logging
    testing-policy.md       ← test type matrix, unit/integration/migration rules
    ui-visual-testing.md    ← component, page, a11y checks
    definition-of-done.md   ← hard gates, per-change-type checklists, quality thresholds
  skills/                   ← module interface summaries (created during work)
  tasks/                    ← execution task files (created during work)
  memory/                   ← multi-session context snapshots (created for EPICs)
docs/
  ARCHITECTURE.md           ← system architecture (filled progressively)
  CUTOFF.md                 ← module registry — what is documented vs not
  DECISIONS.md              ← key architecture decisions and rationale
  CHANGELOG.md              ← completed EPICs log
```

---

## Daily workflow

```text
1. Write requirement
   → Claude triages (TRIVIAL / SIMPLE / STANDARD / EPIC)

2. TRIVIAL: Claude implements directly — no task file
   SIMPLE:   Claude writes 2-section task note → paste into Codex
   STANDARD/EPIC: Claude generates task files + execution plan

3. Paste Codex group → Codex panel   (code edits)
   Paste Cline group → Cline terminal (shell tasks, if any)

4. Review diff → commit using batch commit message → push
```

---

## Key resources

| Resource | Purpose |
|---|---|
| [AGENTS.md](AGENTS.md) | Rules, triage, standards map, auth, error handling, build commands |
| [.ai/routing.md](.ai/routing.md) | Role map, executor guide, triage flow |
| [docs/ARCHITECTURE.md](docs/ARCHITECTURE.md) | System architecture, runbook patterns |
| [docs/CUTOFF.md](docs/CUTOFF.md) | What is documented vs what exists only in code |
| [.ai/standards/](.ai/standards/) | Code conventions, security, testing, UI, definition of done |
