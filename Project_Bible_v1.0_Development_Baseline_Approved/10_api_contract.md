# 10 — API CONTRACT

## Purpose
This document is the integration contract between frontend, backend, AI, and other services.

Do not invent endpoints. If an endpoint is missing, request an API-contract change.

## Endpoint Template

### POST /api/[name]

**Purpose:**  
[...]

**Request:**
```json
{
  "field": "type"
}
```

**Response:**
```json
{
  "success": true,
  "data": {}
}
```

**Errors:**
- 400:
- 401:
- 404:
- 500:

**Authentication:**  
[...]

**Notes:**  
[...]

## Contract Rules
- Request and response shapes are authoritative.
- Backend and frontend must agree before implementation.
- Breaking changes require team approval.
- AI agents must not silently alter contracts.
