# SKILLS-TODO.md — Tech stack registry

Auto-filled by repo-scan. Updated by Claude during work when new info is discovered.

## Rule for Claude
- ❓ = unknown. If a task requires this info: ask human once, then fill here + update relevant skill file.
- ✅ = confirmed. Use directly.
- When human answers an ❓: update status to ✅ and fill value immediately.

---

## Stack

| Role | Status | Value |
|---|---|---|
| Primary language | ❓ | — |
| Runtime / framework | ❓ | — |
| Frontend framework | ❓ | — |
| CSS approach | ❓ | — |
| Database | ❓ | — |
| ORM / query builder | ❓ | — |
| Package manager | ❓ | — |
| Build tool | ❓ | — |
| Test framework | ❓ | — |
| Auth pattern | ❓ | — |
| Deployment target | ❓ | — |

---

## Module skill files

| Module | Skill file | Status |
|---|---|---|
| _filled during work_ | `.ai/skills/<module>.md` | ❓ |

---

## How Claude fills this file

**From repo-scan:**
1. Detect `package.json` / `*.csproj` / `pyproject.toml` / etc. → fill Language + Package manager
2. Detect framework deps → fill Runtime / framework
3. Detect test deps → fill Test framework
4. Detect auth middleware → fill Auth pattern

**During work (when ❓ is encountered):**
1. Stop task
2. Ask human: "What is `<role>` for this project?"
3. Human answers
4. Fill value here → update status to ✅
5. Update `AGENTS.md` relevant section
6. Continue task
