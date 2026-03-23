---
name: endpoint-handoff-doc
description: Generate frontend-only endpoint handoff documentation by scanning backend route/controller/service/OpenAPI code. Use when a user asks how to call an endpoint from a client app, needs request/response/error contracts for implementation, or wants copy-paste integration context for another project agent.
---

# Endpoint Handoff Doc

## Goal
Produce one concise markdown handoff document per endpoint so another agent can implement the client integration quickly.

## Required Behavior
- Treat the task as documentation-first work.
- Do not modify backend code unless the user explicitly asks for code changes.
- Verify behavioral claims from backend source files.
- Keep final output frontend-focused only.
- Exclude backend-internal sections from the final output, including:
- source file inventories,
- OpenAPI/runtime mismatch audits,
- environment variable names unless directly required by frontend behavior.

## Endpoint Discovery Workflow
1. Identify the endpoint target.
- If the user provides a path (example `/api/onetimes`), locate the route under `api/**`.
- If the user provides only a feature name, search route files with `rg` and pick the best match.

2. Resolve route -> controller -> service internally.
- Read route wiring and controller handler.
- Read validation and response mapping in the controller.
- Read service behavior that affects frontend-observable responses.

3. Extract frontend-relevant contract.
- Method/path and expected headers.
- Request schema and field constraints.
- Success/error statuses and payload shapes.
- Side effects and retry/idempotency implications.

4. Synthesize frontend implementation guidance.
- What to send.
- What to expect back.
- How to handle failures safely in UI.

## What To Extract (Checklist)
- Public method and path.
- Required and optional request fields.
- Validation rules and coercion behavior that affect client payloads.
- Success statuses and response payload schema.
- Error statuses and error payload schema/messages.
- CORS/auth expectations relevant to frontend callers.
- Idempotency/side-effect notes when applicable.
- One valid request example and one validation-failure example.

## Output Rules
1. Always produce endpoint documentation, never a code diff summary.
2. Use the template at `references/frontend-endpoint-handoff-template.md`.
3. Fill every section; if unknown, write `Not found in current backend implementation`.
4. Keep the response implementation-ready and concise.
5. Do not include backend file references in the final output unless the user explicitly asks for them.

## Suggested Commands
- `rg --files api src`
- `rg -n "withCors\(|sendError\(|sendJson\(|method !==|Allow|parseJsonBody|fetch\(" api src`
- `sed -n 'start,endp' <file>`

## Resource Usage
- Read `references/frontend-endpoint-handoff-template.md` before writing the final response.
- Reuse template headings verbatim for consistency.
