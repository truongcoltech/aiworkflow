# Security Standards — V1.0

## Workspace isolation (apply to every DB query touching user data)

- Every query must filter by `workspaceId`.
- `workspaceId` must come from authenticated session — never from request body.
- RAG retrieval must scope to `workspaceId` — no cross-workspace chunk access.
- Agent runs must validate `workspaceId` matches `articleId`'s workspace before executing.

## Input validation

- Validate at system boundaries: API endpoints, queue job inputs, webhook handlers.
- Never trust client-supplied entity IDs without verifying workspace ownership.
- Validate enum values explicitly before use in DB queries or agent inputs.
- File uploads: validate MIME type + extension + max size before processing.

## Authentication and authorization

- JWT verification in middleware — not inside route handlers.
- Role checks are explicit per endpoint — "authenticated" does not imply "authorized."
- API tokens (Strapi, publish targets) must be encrypted at rest before storage.
- `publishTarget.authConfig` must be encrypted before persisting to DB.

## Secret handling

- No secrets in: source code, git history, logs, error messages, API responses.
- `ANTHROPIC_API_KEY` and all secrets live in env only — never in code or DB plaintext.
- Application validates required env vars at startup — fail fast if missing.

## Injection prevention

- No string interpolation in DB queries — parameterized queries or ORM always.
- No user input passed to shell commands.
- User-supplied content stored in DB must be sanitized if rendered as HTML.

## Output encoding

- All user-generated content rendered in frontend must be HTML-escaped.
- Article body must be sanitized before browser rendering — no raw `innerHTML` with untrusted content.
- CMS/Strapi API responses must not be passed to DOM without sanitization.

## SSRF prevention

- External URL fetching (research agent web sources) must validate against a per-workspace allowlist.
- No arbitrary URL fetch from user input without an allowlist check.

## Audit logging

- Write to `AuditLog` for: approve/reject, publish, retry, config change, permission change.
- Required fields: `actorUserId`, `entityType`, `entityId`, `action`, `beforeSnapshot`, `afterSnapshot`.
- Never silently overwrite article content — create `ArticleVersion` before any major overwrite.

## Checklist (required for all safety override changes)

- [ ] workspaceId scoping verified on all new queries
- [ ] No new secrets in code or logs
- [ ] Auth/authz enforced in middleware, not handler
- [ ] Sensitive config fields encrypted before persistence
- [ ] AuditLog entry created for state changes
- [ ] Input validation at system boundary
- [ ] No raw SQL string interpolation
- [ ] Classified STANDARD or above (no TRIVIAL/SIMPLE for safety override changes)
