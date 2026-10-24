# 08 — ARCHITECTURE

## Status
PROPOSED / NOT FINAL-LOCKED. Exact implementation architecture and technology versions remain undecided.

## Proposed Technology Roles Discussed
- NVIDIA Nemotron: proposed for reasoning.
- Nebius Token Factory: proposed for model inference/runtime.
- Tavily: proposed for finding and verifying changing external information.
- OpenShell / NemoClaw: proposed for secure agent actions if suitable.
- Application responsibilities discussed: Decision Memory, Context Graph, Assumption Engine, and UI.

These are proposed roles discussed for evaluation, not a finalized stack or commitment to specific models/versions.

## Conceptual System Flow
```text
User / Project Context
        ↓
Decision Memory + Current Project State
        ↓
Selected Assumption Watch
        ↓
External Change Detection and Verification
        ↓
Decision Health + Impact Reasoning
        ↓
Alternative Research / Recovery Plan
        ↓
AUTO / ASK / BLOCK Permission Check
        ↓
Approved Action (when permitted)
        ↓
Result Verification + Context/Timeline Update
```

## Approved Stack
- Frontend: Undecided
- Backend: Undecided
- AI model/version: Undecided
- Inference/runtime implementation: Undecided
- Database: Undecided
- Authentication: Undecided
- Deployment: Undecided
- Testing stack: Undecided

## Architecture Rules
- Do not treat proposed tools as finalized decisions.
- Do not introduce frameworks, services, models, or integrations without approval.
- Do not create duplicate services or databases.
- Respect module boundaries and approved contracts.
- A genuine technical blocker may justify only the minimum necessary scope change; record the reason, approval, and affected features.


## Architecture Template and Team Ownership

The actual approved stack and architecture are not yet finalized. Keep these fields as placeholders until the team explicitly approves them.

| Area | Approved choice |
|---|---|
| Frontend | Undecided |
| Backend | Undecided |
| AI/ML | Undecided |
| Database | Undecided |
| Authentication | Undecided |
| Deployment | Undecided |
| Testing | Undecided |

| Module | Owner | Allowed AI tools | Primary path |
|---|---|---|---|
| Frontend | Undecided | Undecided | Undecided |
| Backend | Undecided | Undecided | Undecided |
| AI | Undecided | Undecided | Undecided |
| Database | Undecided | Undecided | Undecided |
| Integration | Undecided | Undecided | Shared / to be confirmed |

Do not treat the placeholder values above as assignments or approvals.

## Architecture Rules
- Reuse the existing approved architecture.
- Do not introduce a new framework without approval.
- Do not create a second backend or database.
- Do not duplicate authentication.
- Respect module boundaries.
- Change shared contracts deliberately and document the impact.

## Approval & Action Safety Readiness (Working Draft)
The existing conceptual flow remains sufficient; no additional service or vendor is justified by the four working decisions alone. Before end-to-end action execution is implemented, define the supported action contract, identity/role authorization contract, initial risk-to-action mapping, action-specific verification source, durable audit persistence semantics, approval/version binding, and unknown-outcome reconciliation behavior. Exact technologies and contracts remain undecided. See `docs/19_implementation_readiness_review.md`.

## Integration Principle
Modules may be developed independently, but integration must use approved interfaces, API contracts, shared types, and controlled pull requests. The team's exact ownership assignments and workflow remain undecided.
