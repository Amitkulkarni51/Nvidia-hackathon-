# 16 — INTEGRATION PROTOCOL

## Purpose
Define how independently developed modules become one complete product.

## Integration Model

```text
FRONTEND
   ↓
API CONTRACT
   ↓
BACKEND
   ↓
AI SERVICE
   ↓
DATABASE
```

Use the actual approved architecture from `docs/08_architecture.md`.

## Integration Owner
Name:
[Member]

Responsibilities:
- Coordinate integration.
- Review cross-module changes.
- Verify contracts.
- Resolve merge/integration conflicts with the relevant owner.
- Ensure the final demo path works end-to-end.

## Integration Rules

### 1. Contracts First
Use:
- `docs/10_api_contract.md`
- `docs/09_database.md`
- `docs/08_architecture.md`

These define how modules communicate.

### 2. No Silent Contract Changes
A developer or AI must not silently:
- rename an API endpoint
- change request fields
- change response fields
- change authentication behavior
- change database schema
- change shared interfaces

A contract change must be documented and approved.

### 3. Module Independence
Each member should implement their module against the approved contracts.

Example:

```text
Frontend
   ↓
expects POST /api/analyze
   ↓
Backend implements POST /api/analyze
```

If the endpoint does not exist yet, the frontend agent should not invent a different endpoint.

### 4. Cross-Module Requests
If Member A needs Member B to change something:

```text
Need discovered
   ↓
Create request / communicate dependency
   ↓
Explain exact reason
   ↓
Relevant owner implements change
   ↓
Contract/docs updated if required
   ↓
Integration test
```

### 5. Shared Files
For files used by multiple modules:
- Identify an owner.
- Avoid simultaneous AI editing where possible.
- Review every shared-file change carefully.

## Git Workflow

Recommended:

```text
main
  ↓
feature branch
  ↓
AI implementation
  ↓
local checks
  ↓
Pull Request
  ↓
review
  ↓
merge
```

## Integration Order

Use the order appropriate to the architecture. A common sequence is:

1. Database/schema foundation
2. Backend/API
3. AI service
4. Frontend integration
5. End-to-end testing
6. Demo polish

Parallel development is allowed, but contracts must be defined early.

## Integration Checklist

### Frontend ↔ Backend
- [ ] Correct endpoint
- [ ] Correct request
- [ ] Correct response
- [ ] Correct authentication
- [ ] Correct error handling

### Backend ↔ AI
- [ ] Correct input
- [ ] Correct output
- [ ] Correct error behavior
- [ ] Correct environment configuration

### Backend ↔ Database
- [ ] Schema matches implementation
- [ ] Migrations are applied where applicable
- [ ] Queries match approved fields

### End-to-End
- [ ] Main user journey works
- [ ] Loading states work
- [ ] Error states work
- [ ] No broken links/routes
- [ ] No secrets exposed
- [ ] Existing functionality remains intact

## Conflict Resolution

When code conflicts:

1. Check the Project Bible.
2. Check the latest approved decision.
3. Check the relevant contract.
4. Ask the relevant module owner.
5. Prefer the smallest change that restores the approved behavior.
6. Record a new decision if the architecture/product genuinely changes.

Do not resolve conflicts by allowing an AI to arbitrarily rewrite both implementations.

## Final Integration Gate

Before the hackathon demo:

- [ ] Bible is consistent with the final product.
- [ ] All required P0 features work.
- [ ] API contracts are satisfied.
- [ ] Database works.
- [ ] AI service works.
- [ ] Frontend works.
- [ ] Main end-to-end flow works.
- [ ] Critical errors are handled.
- [ ] Repository is clean enough to demo.
