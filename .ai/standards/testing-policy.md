# Testing Policy — V1.0

## Test type matrix

| Change type | Unit | Integration | Contract | Migration |
|---|---|---|---|---|
| New service method | ✅ required | — | — | — |
| New API endpoint | ✅ auth + validation | ✅ happy path | — | — |
| New agent | ✅ output shape | ✅ mocked Anthropic | — | — |
| New BullMQ worker | ✅ dispatch logic | ✅ queue behavior | — | — |
| New DB table / column | — | — | — | ✅ required |
| Shared type / contract change | ✅ all callers compile | — | ✅ required | — |
| Bug fix | ✅ regression required | — | — | — |
| Auth / permission change | ✅ | ✅ | ✅ | — |
| Publish connector | ✅ | ✅ mocked target | — | — |

## Unit test rules

- Mock all external calls: DB, Redis, Anthropic API, HTTP.
- Never hit a real DB or real external API in unit tests.
- Test: happy path, validation errors, boundary conditions, auth failures.
- Agent unit tests must assert: output shape validity, `AgentResult.success` flag, error case handling.

## Integration test rules

- Use a real test DB — never mock the DB in integration tests.
  (Mocked-DB tests passed while prod migrations failed in the past — don't repeat this.)
- Use real Redis for queue integration tests.
- Mock external HTTP (Anthropic, publish targets, web sources).
- New API endpoints: test 401 (no token), 403 (wrong workspace), happy path.

## Migration tests

- Every migration must be tested: apply runs cleanly, spot-check row counts and schema shape.
- Document explicitly if a migration is irreversible.

## Contract tests

- Required when a type exported from `shared/types/` changes.
- All modules importing the changed type must compile and their tests must pass after the change.

## Regression rule

- Bug fixes must include a test that would have caught the bug before the fix.
- Skipping a required test must be explained in DONE WHEN with explicit justification.

## Test location

| Type | Location |
|---|---|
| Unit | Co-located `<file>.test.ts` or `__tests__/` adjacent to source |
| Integration | `tests/integration/<module>/` |
| Migration | `tests/migrations/` |

## Running tests

See `AGENTS.md` build commands section (filled after repo-scan).
