# Frontend Endpoint Handoff: `<METHOD> <PATH>`

## 1. Endpoint Summary
- Purpose:
- Primary consumer:
- Backend-managed concerns relevant to frontend (if any):

## 2. HTTP Contract
- Method:
- Path:
- Required headers:
- Content-Type:
- Auth expectations for frontend caller:

## 3. Request Schema
| Field | Location | Required | Type | Validation / Constraints | Notes |
|---|---|---|---|---|---|
| | body/query/path | yes/no | | | |

## 4. Request Examples
### 4.1 Valid Example
```bash
curl -X <METHOD> "<BASE_URL><PATH>" \
  -H "Content-Type: application/json" \
  -d '<JSON_BODY>'
```

### 4.2 Validation Failure Example
```bash
curl -X <METHOD> "<BASE_URL><PATH>" \
  -H "Content-Type: application/json" \
  -d '<INTENTIONALLY_INVALID_JSON_BODY>'
```

## 5. Response Schema By Status
| Status | Shape | When Returned | Frontend Handling |
|---|---|---|---|
| 2xx | | | |
| 4xx | | | |
| 5xx | | | |

## 6. Success Response Example
```json
{}
```

## 7. Error Response Examples
### 7.1 Validation Error Example
```json
{
  "error": ""
}
```

### 7.2 Server/Upstream Error Example
```json
{
  "error": "",
  "details": {}
}
```

## 8. Validation And Behavior Notes
- Date/time parsing behavior:
- Numeric ID parsing/coercion behavior:
- Nullable fields in response:
- Any side effects or non-idempotent behavior:

## 9. Frontend Implementation Notes
- Recommended request builder inputs:
- Recommended TypeScript interfaces:
- Error handling policy by status:
- Retry guidance:
- Logging/telemetry hints:

## 10. QA Checklist For Frontend Agent
- [ ] Happy path call returns expected success payload.
- [ ] Validation error path surfaces backend message to UI.
- [ ] Unknown/server error path handled with fallback UX.
- [ ] Nullable fields handled safely in rendering.
- [ ] Date fields parsed without timezone regressions.
