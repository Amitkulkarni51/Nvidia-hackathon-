# 09 — DATABASE

## Status
CONCEPTUAL DATA REQUIREMENTS ONLY — no database technology or schema has been approved.

## Decision Memory Record
The following record fields were explicitly discussed:
- Decision
- Reason
- Assumptions
- Evidence
- Date
- Related Project/Goal

The AI drafts the record; the user reviews and confirms it before saving.

## Other Conceptual Data Needs Discussed
- Selected assumptions linked to important decisions for monitoring.
- Current project state alongside remembered decisions.
- Decision health state: `HEALTHY`, `AT RISK`, or `INVALID`.
- Decision Timeline events: decisions, assumption changes, health changes, actions and approvals, outcomes.
- Timeline correction history, so corrected information can be shown as current while preserving the correction history.
- Timeline personal notes.
- Action history: attempted action, reason/context, permission/approval status, execution details, verification result, and failure/recovery details.
- Approval/action safety conceptual history: action/request version and scope; authority/permission evidence; risk classification with rationale/evidence references; approval bound to the reviewed version and material parameters; verification plan and result; audit-persistence status; `UNKNOWN / UNVERIFIED` outcome and reconciliation findings; correction/history links. These are conceptual data needs only, not approved columns or tables.
- Timeline deletion undo for 30 seconds.

## Not Yet Defined
- Database technology.
- Tables, columns beyond the explicitly discussed conceptual record fields, identifiers, and relationships.
- Retention/storage mechanics for deleted timeline entries.
- Exact data types, indexes, migrations, and access controls.

Do not turn the conceptual data needs into a concrete schema without approval.


## Schema Template — Not Yet Finalized

Do not treat the conceptual needs above as an approved physical schema. The following are placeholders to complete after database technology and schema are approved.

### users (if required by the approved product design)
| Column | Type | Required | Description |
|---|---|---|---|
| Undecided | Undecided | Undecided | No user table or fields have been approved |

### Additional tables and relationships
Undecided. Do not invent tables, columns, identifiers, or relationships.

## Database Rules
- Do not invent database fields.
- Do not change schema silently.
- Schema changes require a documented decision and migration.
- Never commit production secrets.

## Migration Policy
All schema changes must be represented in version-controlled migrations where the chosen database workflow supports migrations.
