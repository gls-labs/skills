---
name: sitka-endpoint-layered-architecture
description: Build or refactor API endpoints in sitka-seafood-market-vercel-functions with strict route/controller/service/utils/docs layering. Use when creating a new endpoint, changing endpoint behavior, integrating an upstream API, or reviewing endpoint quality. Enforce thin routes with withCors, controller-only HTTP orchestration, service-only upstream/business logic, utility reuse in src/utils (generic helpers in src/utils/common.ts), OpenAPI updates in src/openapi.ts, TypeScript validation, and curl smoke tests.
---

# Sitka Endpoint Layered Architecture

Implement endpoints with clear separation of concerns and zero duplicated helper logic.

## Non-Negotiable Rules

1. Keep route files thin and limited to `withCors(handler)` wiring.
2. Keep controllers limited to HTTP orchestration: method guards, parsing, validation, service calls, and error mapping.
3. Keep services limited to business logic and upstream integrations (`fetch`, auth headers, URL composition, upstream response mapping).
4. Keep `VercelRequest` and `VercelResponse` usage inside controllers only.
5. Reuse existing utilities before adding logic in controllers or services.
6. Put generic reusable helpers in `src/utils/common.ts`.
7. Put non-generic reusable helpers in a dedicated utility file under `src/utils/<domain>.ts`.
8. Do not duplicate parsing/validation/normalization logic across endpoints.
9. Add every new public endpoint to `src/openapi.ts`.
10. Finish with `npx tsc --noEmit` and curl smoke tests.

## Layer Contract

1. Route layer: `api/**`
- Expose endpoint path.
- Apply CORS wrapper.
- Do not place business logic here.

2. Controller layer: `src/controllers/**`
- Validate HTTP method and set `Allow` header when needed.
- Parse query/body/path input.
- Validate input and return `400` on bad input.
- Call services.
- Map typed service errors to response status/messages.
- Use `sendJson` and `sendError` from `src/utils/http.ts`.

3. Service layer: `src/services/**`
- Validate required environment configuration.
- Call upstream APIs.
- Parse upstream payloads using shared helpers (for example `parseApiResponse`).
- Convert payloads to app-level shape.
- Throw typed errors (`Missing<Provider>ConfigError`, `<Provider>ApiError`, `<Domain>InputError`).

4. Utility layer: `src/utils/**`
- Keep helpers reusable and side-effect-light.
- Prefer existing helpers: `getQueryValue`, `parseApiResponse`, `parseJsonBody`, `isRecord`, `safeStringify`.
- Add new helper only when logic will be reused or when extraction materially improves clarity.

5. API docs layer: `src/openapi.ts`
- Add method/path contract.
- Add parameters and request body schema.
- Add success and error responses.
- Add realistic examples.
- Add appropriate tag.

## Required Build Order

1. Define endpoint contract first.
- Lock method, path, required fields, response schema, and error statuses.

2. Create or update service functions.
- Add typed errors.
- Implement upstream/business logic.
- Keep HTTP framework types out.

3. Create controller.
- Guard method first.
- Parse and validate input with shared helpers.
- Call services.
- Map typed errors to HTTP responses.

4. Create thin route.
- Export `withCors(controllerHandler)` only.

5. Update OpenAPI.
- Keep implementation and contract aligned.

6. Validate and smoke test.
- Run `npx tsc --noEmit`.
- Run endpoint-specific curl test(s).

## Utility Reuse Decision Rules

1. Reuse first.
- Search existing utility functions before writing new logic.

2. Classify helper scope.
- If logic is generic across domains, add it to `src/utils/common.ts`.
- If logic is domain-specific but reusable, add `src/utils/<domain>.ts`.

3. Keep controllers and services focused.
- If a controller or service starts accumulating generic transform/parse helpers, extract them to utils.

4. Avoid endpoint-level copy/paste.
- When implementing a similar endpoint, reuse existing service/utility modules instead of cloning logic.

## Input Validation Rules

1. Parse query params via `getQueryValue`.
2. Validate numeric ids with `/^[0-9]+$/`.
3. Accept JSON body as string or object.
4. Parse JSON safely (`parseJsonBody` + shape checks such as `isRecord`).
5. Return `400` for invalid payloads.

## Error Mapping Rules

1. Service throws typed errors for known cases.
- Missing config.
- Upstream API error with status/details.
- Domain input errors.

2. Controller maps typed errors to status codes.
- Return explicit `4xx/5xx` for known failures.
- Keep a final catch-all `500` fallback.

## File Placement Rules

1. Route: `api/<domain>/<endpoint>.ts` or `api/<endpoint>.ts`
2. Controller: `src/controllers/<domain>Controller.ts`
3. Service: `src/services/<integration-or-domain>.ts`
4. Utility: `src/utils/<helper>.ts`

## Definition Of Done

1. Route/controller/service/utils follow layer boundaries.
2. No duplicated helper logic across endpoints.
3. OpenAPI entry added/updated.
4. `npx tsc --noEmit` passes.
5. Curl smoke tests documented in the final response.
6. Final response lists changed files and includes test commands.
