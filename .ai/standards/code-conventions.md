# Code Conventions — V1.0
<!-- Stack: Node.js + TypeScript, Next.js 14 App Router, Tailwind -->

## TypeScript

- Strict mode always on. `any` only when unavoidable — must have inline comment explaining why.
- Prefer `unknown` over `any` for external or untyped data.
- Export types from `shared/types/index.ts` — no scattered inline exports across modules.
- No `Promise.all` for operations with sequential dependencies — use sequential `await`.
- Interface prefix `I` not used. Plain names: `Article`, `AgentResult`, `KnowledgeChunk`.

## Naming

| Artifact | Convention |
|---|---|
| Files | kebab-case (`article.service.ts`, `topic.agent.ts`) |
| Classes | PascalCase |
| Functions / variables | camelCase |
| Constants | UPPER_SNAKE_CASE |
| DB columns | snake_case |
| Enums | PascalCase key + PascalCase value |
| URL paths | kebab-case |

## Module structure

- Each module has a single entry point: `<module>.service.ts` or `index.ts`.
- No circular imports between modules.
- Domain logic in service layer — not in controllers, handlers, or workers.
- Workers dispatch to agents. Agents do not call workers. No circular agent→worker→agent flow.
- `shared/` exports types and utils only — no business logic in shared.

## Error handling

| Context | Rule |
|---|---|
| Agents | Wrap in try/catch. Write to `AgentRun.errorMessage`. Return `AgentResult { success: false }`. |
| API handlers | Return `{ error, message, statusCode }`. Log full stack server-side. |
| Workers | Log `{ jobId, error, stack }` on every failure. Never swallow. |
| General | No naked `throw` at module boundary. No empty catch blocks. |

## Logging

- Include correlation ID (`articleId` or `jobId`) on every log in agent/worker context.
- Structured logs (JSON) — no `console.log` in production code paths.
- Log levels: `info` for state transitions, `error` for failures, `debug` for verbose agent output.

## Comments

Write a comment only when WHY is non-obvious: hidden constraint, subtle invariant, specific bug workaround.
Never explain what the code does. Never reference the task, PR, or callers.

## Abstraction limits

- Three similar lines is better than a premature abstraction.
- No helper extraction unless used in 3+ places.
- No feature flags or backwards-compat shims — change the code directly.

## AI / agent specific

- No LangChain, LlamaIndex, or AI orchestration frameworks. Direct Anthropic SDK only.
- Prompt cache must be enabled: `cache_control: { type: 'ephemeral' }` on all system prompts.
- Writer Agent receives `researchSynthesis` struct — never raw crawl data.
- Cap injected knowledge tokens per workspace config — never oversized prompts.
- Model defaults: sub-agents → `claude-haiku-4-5-20251001`, Writer → `claude-sonnet-4-6`.
  Do not change defaults without explicit user approval.
