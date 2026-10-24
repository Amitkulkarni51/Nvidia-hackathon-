# 05 — REQUIREMENTS

## Status
WORKING DRAFT — feature scope reviewed as a recap, not final-locked. Detailed implementation contracts remain undecided.

## Working MVP Features

### FR-001 — Decision Memory
**Description:** Record a project decision together with its rationale and context. The AI drafts a proposed record; the user reviews and confirms it before saving.

**Decision record fields discussed:**
- Decision
- Reason
- Assumptions
- Evidence
- Date
- Related Project/Goal

**Acceptance Criteria:**
- [ ] AI can draft a proposed decision record.
- [ ] User reviews and confirms before the record is saved.
- [ ] No additional decision-record fields are assumed.

### FR-002 — Decision Health Engine
**Description:** Track decision health using three states: `HEALTHY`, `AT RISK`, and `INVALID`.

**Working definitions:**
- `HEALTHY`: available evidence supports that the decision remains suitable and key assumptions appear valid.
- `AT RISK`: warning signs exist or evidence is insufficient; gather more evidence.
- `INVALID`: reliable evidence indicates an important assumption failed or the decision no longer meets its requirements.

These definitions are a working direction; exact wording and evidence thresholds remain undecided.

### FR-003 — Assumption Watch
**Description:** Monitor only selected important assumptions linked to decisions. The AI suggests assumptions; the user confirms which assumptions to monitor. Keep the current project state alongside remembered decisions so changes can be interpreted in project context.

### FR-004 — Impact & Recovery Intelligence
**Description:** Explain what changed and the supporting evidence, which decision is affected and why, the project impact, alternatives that satisfy original constraints, and a recommended recovery plan.

### FR-005 — Controlled Action
**Description:** Use `AUTO / ASK / BLOCK` permission modes, with the following working safety decisions. These remain provisional and are not final-locked.

- **Governance:** The system enforces mandatory safety limits. Authorized project administrators can configure AUTO permissions within those limits, subject to role permissions. Mandatory BLOCK restrictions cannot be bypassed by ordinary project permissions; other blocks are overrideable only where policy explicitly permits it.
- **Risk:** Use four working levels: `LOW`, `MODERATE`, `HIGH`, `CRITICAL`. Exact criteria, thresholds, and action mappings remain undecided.
- **Verification:** Use risk-tiered verification, with stronger evidence and independent checks where appropriate for consequential actions. Verify both execution and intended outcome.
- **Uncertainty:** If risk or required verification is uncertain or insufficient, hold the affected consequential action, gather evidence, and reassess. Approval does not bypass mandatory safety limits or unresolved uncertainty.
- **Unknown outcome:** Mark `UNKNOWN / UNVERIFIED`; reconcile actual state using bounded, authorized evidence before any retry, rollback, repair, or resume. Do not automatically retry, roll back, or repair. If unresolved, pause affected work and explain the known/unknown state.
- **Pre-execution evidence:** Require the exact action, authority/permission evidence, safety assessment, verification plan, and a durably saved audit record. Higher-risk actions require stronger evidence.
- **Approval binding:** Bind approval to the exact reviewed action/version, target, scope, and material parameters. Material changes require revalidation and fresh approval where applicable.
- **Audit persistence:** Do not dispatch until required records are durably saved and current safety/authorization checks remain valid. If persistence recovers after delay, revalidate before dispatch.
- **Action history:** Keep an attributable history of attempt, reason/context, risk assessment, permission/approval, execution, verification, unknown states, and failure/recovery details. Preserve corrections rather than silently overwriting prior records.

**Open details:** Exact AUTO catalogue/boundaries, BLOCK categories and override authority, risk thresholds/action mapping, evidence fields, approval expiry/revocation, material-change rules, authoritative verification/reconciliation sources, audit storage durability/retention, and implementation contracts.

**Proposed acceptance criteria:** See `docs/19_implementation_readiness_review.md`. These criteria are proposed for review, not final-approved.

### FR-006 — Decision Timeline
**Description:** A separate sixth MVP feature providing a chronological project timeline.

**Working scope:**
- Events: decisions, assumption changes, decision-health changes, actions and approvals, outcomes.
- Automatically record relevant project events; users may add personal notes and correct entries.
- Verified events are represented as verified; uncertain items are marked unverified.
- Show corrected information as current while retaining correction history.
- Chronological default view with filters for decisions, assumptions, health changes, actions, approvals, and outcomes.
- Basic event details: title, date/time, short description.
- Users can delete timeline entries and undo deletion for 30 seconds. After that window, undo is unavailable through the normal UI.

## MVP Scope Control
- New ideas go to a Future Development list and are not added to the current MVP.
- If a genuine technical blocker requires a change, make only the minimum necessary change.
- Review the problem, alternatives, and impact before replacing a feature; replacement requires explicit user approval.
- Record the reason, approval, and affected features for any approved scope change.
- The previously discussed out-of-scope items remain out of scope for now: email/calendar assistant, giant life-memory system, multiple AI personalities, voice assistant, mobile app, dozens of integrations, custom model training, huge multi-agent system.

## Non-Functional Requirements
Specific performance, security, accessibility, responsiveness, reliability, and measurable acceptance thresholds have not yet been agreed. Do not invent them.

## Unspecified Contracts
API endpoints, exact database technology/schema, authentication, deployment, and detailed technical acceptance criteria are not yet approved. Do not infer them from this document.
