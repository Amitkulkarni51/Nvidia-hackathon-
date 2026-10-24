# 18 — APPROVAL & ACTION SAFETY: CONSISTENCY CHECK AND POLICY MATRIX

**Status: WORKING DRAFT — NOT FINAL-LOCKED.** This document operationalizes the consolidated `docs/17_approval_action_safety.md` into a reviewable policy matrix. It does not approve implementation choices, define unapproved thresholds, or finalize the policy.

## 1. Consistency check

### Result
No direct contradiction was found between the consolidated safety draft and the current Controlled Action descriptions in `05_requirements.md`, `06_user_flows.md`, `07_feature_specs.md`, `08_architecture.md`, `09_database.md`, `12_decisions.md`, and `ai-context/PROJECT_CONTEXT.md` on the main product rules:

- Permission modes remain `AUTO / ASK / BLOCK`.
- AUTO is bounded by pre-approved limits.
- ASK requires informed, explicit approval where applicable.
- BLOCK categories may be permanent or overrideable, but mandatory prohibitions and unresolved safety holds cannot be bypassed.
- Actions must be checked before execution; execution and intended outcome must be verified.
- Failed actions are not automatically retried; recovery must follow the applicable authorization process.
- Audit history, correction history, and uncertain states must be preserved.
- All decisions remain working decisions until explicit final approval.
- Reviews 1008–1012 remain unanswered and must not be treated as approved.

### Items to keep visible during implementation

1. **Backup authority:** Review 32 selected a predefined authorized backup authority when the primary decision-maker is unavailable; if no such backup is available, work stays paused. This specific decision is preserved in the consolidated draft's exceptions section and should also be represented in the eventual governance configuration.
2. **Retry semantics:** The product-level rule is “no automatic retry after a failed action.” Implementation may need transport-level retry behavior, but it must not silently repeat consequential side effects or be treated as permission to retry a failed action. Any such mechanism needs explicit design and tests before approval.
3. **Audit durability:** Required approval/risk records must persist before execution. The storage technology, durability guarantee, and recovery procedure remain undecided.
4. **Override configuration:** The categories that are permanently blocked versus overrideable, and who can configure them, remain undecided beyond the working principles in the safety draft.
5. **Risk taxonomy:** Risk levels, thresholds, evidence sufficiency, timeouts, escalation deadlines, and action-specific verification requirements are not yet defined.
6. **Multimodal decisions:** Reviews 1008–1012 are still pending. The policy must not represent their recommended options as approved.
7. **Implementation contracts:** APIs, schemas, authentication/session design, cryptographic mechanisms, provider versions, sandbox technology, and retention mechanics remain unapproved.

### Source alignment notes
- `05_requirements.md` and `07_feature_specs.md` state that detailed acceptance criteria and technical contracts are not final. This matrix is therefore a proposed test-design aid, not a claim that those criteria have already been approved.
- `06_user_flows.md` says failed actions should not be retried automatically and recovery requires approval. This matrix preserves that rule.
- `08_architecture.md` and `ai-context/PROJECT_CONTEXT.md` state that the stack and exact implementation are undecided. No technology choices are introduced here.
- `09_database.md` records conceptual audit/history needs but does not approve a physical schema. No schema is specified here.

## 2. Policy matrix

| ID | Trigger / condition | Required control | Hold / failure behavior | Minimum evidence to retain | Status |
|---|---|---|---|---|---|
| P-01 | Any action is proposed | Canonicalize action, target, parameters, scope, environment, dependencies, and expected result; check current policy and authority | Reject or hold if required fields, authority, or scope are missing or ambiguous | Request identity/version, action proposal, policy result, reason | Working rule |
| P-02 | Action is considered for AUTO | Confirm action type is approved for AUTO and within system safety limits and user-configured boundaries | Do not execute under AUTO if eligibility fails; consider ASK only if otherwise permitted; BLOCK unsafe/prohibited actions | Eligibility checks, applicable boundary/config version, disposition | Working rule; exact boundaries undecided |
| P-03 | ASK approval is required | Explain material action details, scope, target, effects, uncertainty, and alternatives; obtain explicit approval from an authorized person bound to the reviewed version | Silence, delivery, acknowledgement, or urgency is not consent; hold until valid approval | Approver identity/authority, exact reviewed request/version, decision, timestamp | Working rule |
| P-04 | BLOCK applies | Determine whether the category is mandatory/permanent or explicitly overrideable under the configured policy | Never bypass mandatory prohibition or unresolved safety/integrity hold; if override authority is unclear, remain blocked | Block category, rule version, reason, any authorized override record | Working rule; category configuration undecided |
| P-05 | Risk is uncertain, disputed, or changes materially | Assess with evidence and reasons; reassess before dispatch and after material changes | Apply stricter treatment and hold when unresolved; approval cannot bypass uncertainty | Prior/new risk, evidence, rationale, time, outcome | Working rule; taxonomy/thresholds undecided |
| P-06 | Approval, authority, session, policy, or request version changes | Revalidate identity, role, delegation, scope, approval freshness, revocation, and exact request binding | Hold if approval or authority cannot be verified; obtain fresh approval when required | Request/version, authority evidence, validation outcome | Working rule |
| P-07 | Multi-step workflow has dependencies or parallel steps | Validate each step and its prerequisites; parallelize only when independence and safety are established | Pause affected/dependent steps; continue independent steps only if explicitly defined as safe and authorized | Step graph, completed/in-progress/unknown states, dependency checks | Working rule |
| P-08 | Action fails, is cancelled, times out, crashes, or has unknown outcome | Reconcile actual external state and downstream status before resuming, retrying, rolling back, or repairing | No automatic retry or assumed rollback; preserve unknown status until evidence resolves it | Dispatch/job IDs, state checks, partial effects, failure/cancellation evidence | Working rule |
| P-09 | Model proposes a tool call, code, or downstream instruction | Independently validate tool, arguments, target, permissions, environment, resource limits, and typed handoff contract | Reject/quarantine invalid or ambiguous calls; code execution needs risk-appropriate isolation and side-effect checks | Model output provenance, validation result, tool request, execution record | Working rule |
| P-10 | Evidence conflicts, is stale, missing, or possibly altered | Check source, time, scope, version, transformations, and integrity; use bounded authorized reconciliation | Mark uncertain and hold dependent consequential actions until resolved | Sources, provenance, discrepancy, reconciliation steps/results | Working rule |
| P-11 | Inference output is used for a decision/action | Validate completion status, schema, semantics, business invariants, claim-evidence support, relevant context, and output-to-request association | Mark incomplete/invalid or hold consequential use if validation fails | Model/deployment/config where available, request ID, output status, validation findings | Working rule |
| P-12 | Provider fallback, runtime, routing, cache, or serving config changes | Validate task capability, data class, jurisdiction, contract, freshness, isolation, and material behavior | Do not use an unapproved/incompatible fallback or cache entry; reassess material provider/terms/config changes | Provider/config identity, routing decision, data-handling assessment, test results | Working rule; exact providers/versions undecided |
| P-13 | Streamed, partial, batched, or asynchronous result arrives | Validate completion, sequence/coverage, request/job identity, item mapping, and artifact association | Treat incomplete or mismatched results as provisional/invalid; do not execute partial tool calls | Chunk/item/job IDs, completion metadata, integrity checks, result status | Working rule |
| P-14 | Data is disclosed, imported, exported, transformed, or synced | Validate authorization, purpose, recipients, minimum necessary data, retention, jurisdiction, mapping, provenance, and downstream effects | Hold unauthorized or semantically ambiguous transfer/mapping; quarantine untrusted imported instructions | Scope/recipient, source and destination versions, transformation/mapping, disposition | Working rule |
| P-15 | Incident, alert, assurance claim, or remediation is handled | Preserve traceability; separate detection, delivery, acknowledgement, disposition, remediation, verification, and residual-risk acceptance | Do not close while required verification, affected-decision assessment, or disposition remains unresolved | Incident/claim IDs, evidence, ownership, actions, verification, unresolved risks | Working rule |
| P-16 | Action finishes or new evidence changes project understanding | Verify execution and intended outcome; update decision memory/project state/timeline only from appropriately verified evidence | Keep uncertainty explicit; corrections append history rather than overwrite; no claim of success without evidence | Attempt, approval, execution, verification, outcome, correction links | Working rule |

## 3. Suggested acceptance tests

These are proposed tests derived from the working rules, not approved thresholds or a complete test suite.

1. **Authorization:** A syntactically valid tool call exceeds the authorized scope. Expected: no dispatch; a traceable rejection/hold.
2. **Approval binding:** A parameter or target changes after approval. Expected: previous approval is not silently reused; revalidation and fresh approval when required.
3. **Unknown outcome:** A request times out after dispatch and execution status is unknown. Expected: no automatic repeat; reconcile actual state first.
4. **Partial workflow:** One step fails while another independent step remains. Expected: dependent steps pause; independent work continues only if explicitly safe and authorized.
5. **Audit persistence:** Required approval/risk record cannot be persisted. Expected: no execution.
6. **Evidence conflict:** Two authoritative-looking sources conflict on a material assumption. Expected: preserve both, mark unresolved, hold dependent consequential action.
7. **Semantic validation:** Output passes JSON/schema validation but violates a domain invariant. Expected: reject/quarantine; no downstream consequential use.
8. **Claim support:** A citation exists but does not substantiate its claim. Expected: qualify/remove the claim or verify it independently.
9. **Provider fallback:** Fallback lacks a required modality or structured-output contract. Expected: do not silently approximate; return limitation or approved alternative.
10. **Cross-request isolation:** A result is associated with the wrong request/session. Expected: reject the association and prevent application to the wrong task.
11. **Streaming corruption:** A chunk is missing or reordered. Expected: mark incomplete/invalid until safely reconciled.
12. **Cancellation:** Client cancellation occurs while backend work may continue. Expected: report cancellation as confirmed only after authoritative status supports it.
13. **Data boundary:** Provider route violates a configured jurisdiction or retention requirement. Expected: block that route or use a validated permitted route.
14. **Correction history:** A verified result later requires correction. Expected: show the corrected current state while retaining linked history.
15. **Pending multimodal policy:** Reviews 1008–1012 have no selections. Expected: specification continues to mark these as pending, not approved.

## 4. Decisions still requiring human/team input

Resolve these only when needed for the next implementation milestone:

- AUTO action catalogue, boundaries, and configuration authority.
- Permanent versus overrideable BLOCK categories and authorized override roles.
- Risk taxonomy, thresholds, evidence sufficiency, and escalation deadlines.
- Action-specific verification methods and what happens when verification is unavailable.
- Named primary/backup authority and dispute-resolution process.
- Audit persistence, retention, deletion, and legal-hold implementation.
- Exact API/data contracts, identity/session model, runtime/provider versions, sandboxing, and security mechanisms.
- Reviews 1008–1012.
- Measurable non-functional requirements and release gates.

**Do not final-lock this matrix.** It is intended to make the current working decisions testable and to focus follow-up on implementation-relevant gaps.


## 5. Recorded working decision — Decision 1

**Selected direction:** Hybrid AUTO/BLOCK governance.

- The system enforces mandatory safety limits.
- Authorized project administrators configure AUTO permissions within those limits, subject to role permissions.
- Mandatory BLOCK restrictions cannot be bypassed by ordinary project permissions.
- Other blocks may be overrideable only where policy explicitly permits it and the required authority and informed confirmation are present.

**Implications:** Permission changes need defined scope, accountable ownership, and auditable history. The system must distinguish mandatory policy prohibitions from configurable project restrictions.

**Open details:** AUTO action catalogue; permanent versus overrideable BLOCK categories; override authority; whether permission changes require additional approval; and effects on queued/in-flight actions.

**Status:** WORKING DRAFT — NOT FINAL-LOCKED.


## 6. Recorded working decision — Decision 2

**Selected direction:** B + B + A. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Four working risk levels: `LOW`, `MODERATE`, `HIGH`, `CRITICAL`.
- Risk-tiered verification: stronger evidence and independent checks where appropriate for consequential actions.
- If risk or required verification is uncertain or insufficient, hold the action, gather evidence, and reassess. Approval does not bypass mandatory safety limits or unresolved uncertainty.

**Implications:** Classify based on target, scope, reversibility, potential impact, uncertainty, and current project state. Define success conditions and suitable evidence for each enabled action type. Distinguish execution verification from intended-outcome verification and preserve risk/verification history.

**Open details:** Exact criteria/thresholds; action-to-risk mapping; evidence and independence requirements per tier; authoritative evidence sources; handling unavailable verification; and action types that remain prohibited regardless of score.

**Status:** WORKING DRAFT — NOT FINAL-LOCKED.


## 7. Recorded working decision — Decision 3

**Selected direction:** A — Reconcile actual state first. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Mark unverified outcomes `UNKNOWN / UNVERIFIED`; do not infer success or failure.
- Perform bounded, authorized reconciliation before any retry, rollback, repair, or resume.
- Do not automatically retry, roll back, or repair.
- Preserve the last verified state and distinguish completed, failed, in-progress, and uncertain steps.
- If the result remains unknown, stop affected work, explain uncertainty, and propose recovery.
- Consequential recovery requires applicable authorization, approval, and current safety checks.
- Append new verified findings and preserve the original uncertainty/history.

**Implications:** A timeout is not proof of failure. Execution status and outcome verification are distinct. Reconciliation must be bounded, authorized, and non-consequential. The user-facing status must explain uncertainty and last verified state.

**Open details:** Permitted reconciliation sources by integration; bounds and escalation; ownership/time limits for unresolved unknowns; authoritative verification methods; recovery classification; and handling of systems that cannot expose reliable state.

**Status:** WORKING DRAFT — NOT FINAL-LOCKED.


## 8. Recorded working decision — Decision 4

**Selected direction:** B + A + A. **WORKING DRAFT — NOT FINAL-LOCKED.**

- **Risk-tiered minimum evidence:** Before execution, require the exact action, authority/permission evidence, safety assessment, verification plan, and durably saved audit record. Higher-risk actions require stronger evidence.
- **Approval binding:** Bind approval to the exact reviewed action/version, target, scope, and material parameters. Material changes trigger revalidation and fresh approval where applicable.
- **Audit persistence failure:** Stop execution until required records are durably saved and current safety/authorization checks remain valid.

**Implications:** Keep approval, evidence sufficiency, persistence, execution, and outcome verification distinct. Durable audit persistence is not proof of successful execution. After a persistence delay, revalidate current policy, risk, authority, approval, request version, and execution status before dispatch.

**Open details:** Required evidence fields and durability guarantees; definition of material changes; approval expiry/revocation rules; risk-specific evidence and verification sources; lost write acknowledgements and uncertain dispatch handling; possible future fallback audit store.

**Status:** WORKING DRAFT — NOT FINAL-LOCKED.


## 9. Implementation readiness review

**Status: WORKING DRAFT — NOT FINAL-LOCKED.**

The four safety decisions fit the current conceptual architecture. No new service, database, agent, framework, or vendor is justified by the decisions alone. End-to-end implementation remains blocked on the affected contracts: supported action shape; identity/role authorization; initial risk-to-action mapping; action-specific verification evidence; durable audit persistence semantics; approval/version binding; and unknown-outcome reconciliation.

Proposed acceptance criteria and a staged implementation sequence are in `docs/19_implementation_readiness_review.md`. They are for review, not final-approved.

## 10. Context Note Update v0.1 — conflict handling

**User-selected direction:** A — Stop and ask. **WORKING DRAFT — NOT FINAL-LOCKED.**

If the target note changes after preparation, pause, show the current and proposed versions, and ask the user how to proceed. Do not silently overwrite, automatically rebase, or merge. Revalidate authorization, policy, request version, and required evidence before resuming. Preserve the conflict and decision history. Exact UI, version/concurrency implementation, and future merge/rebase support remain open.

## 11. Context Note Update v0.1 — atomic pre-write version check

**User-selected direction:** A — Atomic version check. **WORKING DRAFT — NOT FINAL-LOCKED.**

Immediately before saving, the current note version must match the exact approved version, with the comparison and write performed atomically. On mismatch, reject the write, preserve newer content, pause, and follow the stop-and-ask conflict flow. No non-atomic fallback is permitted. If storage cannot support this guarantee, hold implementation of this path until a safe mechanism is selected. Storage/API/versioning details remain open.

## 12. Context Note Update v0.1 — save-timeout reconciliation

**User-selected direction:** A — Reconcile before recovery. **WORKING DRAFT — NOT FINAL-LOCKED.**

A timeout is neither success nor failure. Mark `UNKNOWN / UNVERIFIED`; inspect actual state using bounded, authorized reconciliation. Record success only with authoritative verification. If the state is unchanged or ambiguous, pause; do not automatically retry, roll back, or repair. Preserve the timeout/reconciliation history and revalidate before any newly authorized write. Technical bounds, authoritative read source, and idempotency details remain open.

## 13. Context Note Update v0.1 — duplicate-write prevention

**User-selected direction:** A — Unique Operation ID plus reconciliation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Give each logical update a unique Operation ID. After timeout or lost acknowledgement, reconcile the original operation; never automatically create a new operation or retry. If status cannot be established, pause and ask. An Operation ID alone does not guarantee exactly-once execution. ID lifecycle, authoritative status source, retention, and storage idempotency guarantees remain open.

## 14. Context Note Update v0.1 — unknown-outcome escalation

**User-selected direction:** A — Bounded authorized reconciliation, then inform user and remain paused if status is unknown. **WORKING DRAFT — NOT FINAL-LOCKED.**

Use bounded, authorized reconciliation of the original operation. If status remains unknown, preserve `UNKNOWN / UNVERIFIED`, pause affected work, tell the user what is known and unknown, and ask how to proceed. Never automatically retry, create a new operation, roll back, or repair. Revalidate authorization, policy, expected version, approval binding, and required evidence before later authorized writes/recovery. Reconciliation limits, authoritative source, notification/acknowledgement, and unresolved-case ownership remain open.

## 14. User unavailable while outcome remains unknown

**User-selected direction:** Keep the affected Context Note Update v0.1 action paused until the user returns and provides direction. **WORKING DRAFT — NOT FINAL-LOCKED.**

No automatic further work, retry, rollback, repair, or recovery while the outcome remains `UNKNOWN / UNVERIFIED` and the user is unavailable. Preserve the original operation and uncertainty history. When the user returns, explain known/unknown state and request direction; revalidate applicable controls before any later action.

## 15. Resume revalidation after user returns

**User-selected direction:** Revalidate all relevant current state before resuming. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, recheck original operation status, current note state/version, authorization, applicable policy, approval validity/binding, and required evidence. A user request to continue is not proof of outcome or automatic authorization to repeat a write. If uncertainty persists, remain paused. No automatic new operation, retry, rollback, repair, or recovery.

## 16. Intended note update already applied

**User-selected direction:** Verify, record, and stop. **WORKING DRAFT — NOT FINAL-LOCKED.**

Verify current note content/version against the intended result using authoritative evidence. If verified, record the original operation outcome, preserve timeout/reconciliation history, and do not write again. If the state or evidence is ambiguous, remain paused and explain uncertainty. Separate follow-up changes require their own controls.

## 17. Content matches but version/operation metadata differs

**User-selected direction:** Require both content and version verification; content match alone is insufficient. **WORKING DRAFT — NOT FINAL-LOCKED.**

If content appears to match but expected version or operation metadata differs, remain paused and perform authorized, bounded reconciliation. Preserve the discrepancy and history. If it cannot be authoritatively explained, retain `UNKNOWN / UNVERIFIED` and ask the user. No automatic write, replacement operation, retry, rollback, or repair.

## 18. User-facing uncertainty when evidence is unavailable

**User-selected direction:** Provide a transparent uncertainty report. **WORKING DRAFT — NOT FINAL-LOCKED.**

Report intended action, observed state, missing/unavailable verification evidence, and actions not taken. Keep `UNKNOWN / UNVERIFIED` if evidence is insufficient; preserve history; remain paused pending user direction. Do not automatically resume or recover.

## 19. User-authorized investigation boundaries

**User-selected direction:** A — Bounded read-only investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

When the original operation is `UNKNOWN / UNVERIFIED`, further investigation authorized by the user is bounded and read-only. Use permitted evidence sources, preserve the original operation and evidence history, and distinguish verified findings from uncertainty. Do not write, create a replacement operation, retry, rollback, repair, or recover during investigation. If unresolved within bounds, remain paused and `UNKNOWN / UNVERIFIED`. Consequential follow-up requires separate authorization and applicable checks.

## 20. Evidence source authority and trust

**User-selected direction:** A — Pre-approved authoritative sources only. **WORKING DRAFT — NOT FINAL-LOCKED.**

Context Note Update v0.1 uses only pre-approved authoritative sources relevant to the original operation. An approved source being unavailable does not make an alternative authoritative. Stale, incomplete, unavailable, or conflicting evidence preserves `UNKNOWN / UNVERIFIED` unless required verification is established from an approved source. Preserve traceable evidence history; investigation remains bounded and read-only.

## 21. Conflicting authoritative evidence

**User-selected direction:** A — Preserve conflict and pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

When two pre-approved authoritative sources conflict about the original operation, preserve both observations and available timestamps/provenance, do not silently prefer either, retain `UNKNOWN / UNVERIFIED` if required verification is blocked, pause the affected action, and explain the conflict. Reconciliation remains bounded, read-only, and limited to pre-approved authoritative sources.

## 22. User notification on evidence conflict

**User-selected direction:** A — Notify immediately; suppress duplicate alerts; update on meaningful status changes. **WORKING DRAFT — NOT FINAL-LOCKED.**

When conflicting authoritative evidence blocks required verification and the action is paused, notify the user promptly. Suppress duplicate alerts for the same unresolved conflict without meaningful change; update on meaningful status/evidence changes. Preserve `UNKNOWN / UNVERIFIED` and paused status until required verification is resolved. Minimize sensitive information and preserve notification history.

## 23. Notification delivery failure

**User-selected direction:** A — Record failure, use only explicitly permitted bounded retries, then preserve failure/unknown delivery status and keep the action paused. **WORKING DRAFT — NOT FINAL-LOCKED.**

Record attempts and observed delivery results. Retry only under explicitly permitted bounded rules. If delivery remains unconfirmed, do not claim receipt; preserve failure or `UNKNOWN / UNVERIFIED` delivery status. Keep notification delivery separate from operation status, preserve history, and keep the affected action paused while the operation outcome is unresolved.


## 24. Notification delivery evidence

**User-selected direction:** C — Keep request/provider acceptance, delivery confirmation, and user read/acknowledgement distinct. **WORKING DRAFT — NOT FINAL-LOCKED.**

Record each lifecycle stage only when supported by its corresponding evidence. A send request does not prove provider acceptance; provider acceptance does not prove delivery; delivery does not prove reading or acknowledgement. If a stage cannot be established, retain `UNKNOWN / UNVERIFIED` for that stage. Notification lifecycle status remains separate from the underlying operation/action outcome, and status transitions/evidence are preserved. Provider/channel-specific receipt rules remain open.


## 25. Undeliverable notification fallback

**User-selected direction:** A — Pause and wait as the baseline. **WORKING DRAFT — NOT FINAL-LOCKED.**

When bounded permitted notification attempts end without confirmed delivery, preserve the supported failure or `UNKNOWN / UNVERIFIED` delivery status and attempt history. Do not automatically choose a fallback channel, contact, or escalation route. Keep the affected action paused while its underlying operation outcome remains unresolved, and surface the pending issue clearly when the user returns. Future fallback or escalation requires explicit authorization and applicable policy, permission, privacy, and bounded-attempt checks.

**Consistency check:** Notification failure must not be interpreted as operation failure or success. Delivery status remains distinct from the underlying operation status. No unapproved fallback, contact, or escalation is initiated.

**Still open:** User availability detection, pending-issue surfacing, retention/ownership while waiting, and whether explicitly pre-authorized fallback channels or risk-based escalation should be considered later.


## 26. Surfacing unresolved issues when the user returns

**User-selected direction:** C — Persistent pending-issues panel plus return alert. **WORKING DRAFT — NOT FINAL-LOCKED.**

For unresolved notification-delivery issues, show a prominent alert on user return/session start and keep the issue in a persistent pending-issues panel until resolved or explicitly dismissed under applicable rules. Dismissing the alert is not resolution and does not change delivery or operation status. User return alone does not authorize resumption of a paused action.

**Consistency check:** Known facts, unknown/unverified delivery state, and unresolved operation outcome remain distinct. No success is inferred from the user's return or alert dismissal.

**Still open:** Session/return definition, alert frequency and deduplication, panel ordering, dismissal authority and semantics, retention/ownership.


## 27. Pending issue acknowledgement

**User-selected direction:** B — Acknowledge, but keep unresolved. **WORKING DRAFT — NOT FINAL-LOCKED.**

The user may mark a pending issue as seen/acknowledged. Record acknowledgement separately from delivery status, operation status, and resolution. The issue remains unresolved and visible in the persistent panel. Acknowledgement alone does not verify the outcome or authorize resumption, retry, or recovery.

**Consistency check:** Acknowledgement, notification delivery, operation outcome, and issue resolution remain distinct states. Normal evidence, authorization, and verification requirements continue to apply.

**Still open:** Reversal, display, shared-project authority, exact audit fields.


## 28. Shared-project acknowledgement permissions

**User-selected direction:** C — Any project member may acknowledge; resolution remains separately restricted. **WORKING DRAFT — NOT FINAL-LOCKED.**

Authorized project members may mark a pending issue as seen/acknowledged. Record the actor and acknowledgement event separately. Acknowledgement is not resolution, verification, or authorization for resumption, retry, recovery, or consequential follow-up. Resolution and consequential actions require their own applicable authorization, evidence, and verification.

**Consistency check:** Acknowledgement permission remains separate from issue-resolution and action-execution permissions.

**Still open:** Membership/guest boundaries, actor audit fields, and resolution role model.


## 29. Conflicting instructions in shared projects

**User-selected direction:** C — Role-based authority with pause on ambiguity. **WORKING DRAFT — NOT FINAL-LOCKED.**

Defined roles and permissions determine which members may authorize which actions. When authority is unclear, overlapping, or instructions conflict, pause the affected action and request clarification through the applicable authorized process. One member's instruction does not automatically override another's. Mandatory safety restrictions, evidence requirements, and verification requirements remain in force.

**Consistency check:** Acknowledgement permission is distinct from resolution and action-execution authority. Conflicting instructions cannot silently change an approval or authorize consequential action.

**Still open:** Role hierarchy, conflict resolution/escalation path, conflict criteria, queued/in-flight handling, pending-approval effects.


## 30. Conflicting instructions when the project owner is unavailable

**User-selected direction:** C — Use a delegate if authorized; otherwise pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

A pre-designated delegate may resolve a conflict only if explicit authority covers the specific conflict/action and only within that scope. If no suitable delegate exists or authority/scope is ambiguous, keep the affected action paused and wait for the owner or another properly authorized clarification. Owner unavailability does not automatically transfer authority.

**Consistency check:** Role-based authorization remains in force; membership, seniority, or availability alone does not grant decision authority. Safety, evidence, approval, and verification requirements remain applicable.

**Still open:** Delegate designation/revocation, scope, expiry, owner-unavailability criteria, escalation route, queued/in-flight effects.


## 31. Delegate authority expiry or revocation

**User-selected direction:** C — State-aware authority revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Expiry/revocation blocks new actions relying on that delegation. Pending affected actions pause and authority must be revalidated before resumption. In-flight actions require safe assessment of actual execution state; do not blindly cancel, retry, or roll back. Unknown outcomes use approved bounded reconciliation, and consequential follow-up requires applicable fresh authorization. Preserve delegation, action-state, evidence, and authorization history.

**Consistency check:** Revocation does not prove that an in-flight action stopped or failed. Unknown outcome remains `UNKNOWN / UNVERIFIED`; no unsafe automatic recovery.

**Still open:** Revocation propagation timing, authoritative delegation source, in-flight classes/safe-stop, reauthorization requirements, queued work.


## 32. Previously approved action after delegate revocation

**User-selected direction:** C — Revalidate approval binding and current authority. **WORKING DRAFT — NOT FINAL-LOCKED.**

For an approved action that has not started, revalidate the approver, authority basis, approval scope/version, current policy, target, and action state before dispatch. Do not automatically assume approval remains valid or is invalidated solely because delegation was revoked. If validity or authority is ambiguous, pause and obtain fresh authorization. Preserve original approval and revocation history.

**Consistency check:** Approval binding and current authorization are both required; revocation does not by itself establish execution state or approval validity.

**Still open:** Default effect of revocation on delegated approvals, approval versioning, queued approvals.


## 33. Scope or parameter changes after approval

**User-selected direction:** C — Strict binding with explicit materiality rules. **WORKING DRAFT — NOT FINAL-LOCKED.**

Each approval binds to the exact reviewed action, target, version, scope, and relevant parameters. Explicitly classified non-material changes may follow approved policy rules. Material changes require revalidation and fresh approval where applicable. If materiality or approval applicability is ambiguous, pause and clarify rather than silently reusing approval. Preserve the original and changed requests, classification/revalidation result, and approval history.

**Consistency check:** Approval for one action/version does not silently authorize a materially different action. Mandatory safety and authorization checks remain in force.

**Still open:** Action-specific materiality criteria, who approves criteria, always-material changes, detection/versioning.


## 34. Who classifies material changes after approval?

**User-selected direction:** C — Policy rules plus AI assistance; ambiguity means pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

Approved policy rules are authoritative. AI may identify, compare, and explain differences but must not invent exceptions or override policy boundaries. If no rule clearly covers a change or applicability is ambiguous, pause and request clarification. Preserve the compared versions, differences, applied rule, AI assistance, classification, and approval/revalidation history.

**Consistency check:** AI assists interpretation; approved policy remains the authority for materiality and approval boundaries.

**Still open:** Rule ownership/approval, versioning, escalation for uncovered cases, and recording of AI suggestions.


## 35. Approval validity and expiry

**User-selected direction:** C — Risk-based expiry plus pre-execution revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Approval validity follows risk-based, approved policy rules. Before execution, revalidate current authority, approved action/scope, relevant policy, and required evidence. If approval has expired or validity is ambiguous, pause and obtain fresh approval. Preserve approval time, validity/expiry rule, revalidation evidence, and renewal/fresh-approval history.

**Consistency check:** This decision complements approval binding and delegate-revocation revalidation; neither unchanged scope nor prior approval removes the need for current checks.

**Still open:** Risk-to-expiry mapping, invalidating evidence/policy changes, expiry clock source, notification timing, renewal workflow.


## 36. Policy changes after approval

**User-selected direction:** C — Revalidate current policy before execution. **WORKING DRAFT — NOT FINAL-LOCKED.**

Execution must use current applicable policy, not only the policy at approval time. If a policy update prohibits the action or materially changes approval conditions, pause and follow the required reapproval path. If the change is irrelevant, proceeding is permitted only where documented rules allow it. Preserve approval-time/current policy versions, revalidation outcome, and pause/reapproval history.

**Consistency check:** Complements Decision 31 (risk-based approval validity and pre-execution revalidation) and Decision 29 (strict approval binding/materiality rules).

**Still open:** Relevance/materiality criteria, authoritative policy version source, emergency policy changes, and policy changes during in-flight actions.


## 37. Policy update during an in-flight action

**User-selected direction:** C — Risk-aware pause/containment plus state verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Assess the updated policy's applicability and actual action state. If continuing may be prohibited or unsafe, pause/contain at a feasible safe boundary and verify state. Do not blindly cancel, retry, roll back, or assume stopping succeeded. If uncertainty remains, safely hold where possible and request authorized review. Preserve policy change/version, observed state, verification/containment steps, and review history.

**Consistency check:** Extends Decision 32's current-policy revalidation into in-flight execution and aligns with unknown-outcome and safe-stop principles.

**Still open:** Action-specific safe boundaries, urgent policy-change handling, authority for continuation/recovery, and verification sources for partial completion.


## 38. Action cannot be safely paused

**User-selected direction:** C — Action-specific safe-stop policy plus containment. **WORKING DRAFT — NOT FINAL-LOCKED.**

Use pre-defined action-specific safe-stop rules rather than assuming all actions can safely pause or must always be interrupted. If safe interruption is not possible, avoid unapproved new steps; use permitted containment, monitor/verify actual state, and request authorized review. Completion alone does not establish safety or policy compliance. Preserve applicable rule, policy change, containment steps, observed state, verification evidence, and review history.

**Consistency check:** Extends Decision 33's in-flight policy-change handling and aligns with safe-stop, partial-effect verification, and unknown-outcome principles.

**Still open:** Safe-stop catalogue, permitted containment, verification sources, escalation timing, and post-event recovery authority.


## 39. Recovery authorization after safe-stop

**User-selected direction:** C — Risk- and role-based fresh authorization. **WORKING DRAFT — NOT FINAL-LOCKED.**

Recovery is a separate action and is not automatically authorized by the original action's approval. Validate actual state, current authority, risk, and applicable policy. Consequential recovery requires fresh approval from an appropriately authorized person. Emergency permissions apply only where explicitly defined by policy and within scope. Preserve rationale, state/evidence, authorization, execution, and verification history.

**Consistency check:** Aligns with the safe-stop policy and existing rules that consequential recovery requires authorization; emergency paths cannot be inferred from unavailability alone.

**Still open:** Recovery risk tiers, eligible roles, emergency authority boundaries, approval binding/expiry, and recovery-specific verification requirements.


## 40. Recovery approver unavailable

**User-selected direction:** C — Pre-authorized escalation path. **WORKING DRAFT — NOT FINAL-LOCKED.**

Use a pre-designated, appropriately authorized alternate/emergency role only within explicit policy and scope. Unavailability does not automatically transfer authority to AI or another person. If no valid path exists, safely hold where possible, monitor state, and escalate; do not execute consequential recovery without authorization. Preserve availability status, escalation path, authority checks, state/evidence, and recovery decision history.

**Consistency check:** Complements Decision 35: consequential recovery needs fresh authorization; an alternate is valid only when authority is pre-defined and current.

**Still open:** Eligible roles, scope boundaries, escalation timing, availability detection, and permitted monitoring/containment while waiting.


## 41. Scope of emergency recovery authority

**User-selected direction:** C — Narrow predefined authority plus strict limits. **WORKING DRAFT — NOT FINAL-LOCKED.**

Emergency recovery authority applies only to explicitly authorized recovery actions within defined scope, risk limits, and expiry. Each action requires applicable state/evidence checks and an auditable record. Outside the limits, require fresh authorization or safe hold. Emergency authority is not blanket permission for discretionary action.

**Consistency check:** Complements Decisions 35–36: recovery is a separate action; alternates need pre-authorized authority, and emergency scope is bounded.

**Still open:** Role eligibility, action catalogue, risk thresholds, expiry/revocation behavior, evidence requirements, and post-action review.


## 42. Review after emergency recovery

**User-selected direction:** C — Audit every recovery plus risk-based independent review. **WORKING DRAFT — NOT FINAL-LOCKED.**

Every emergency recovery must be audited and its outcome verified. Higher-risk or exceptional cases require independent human review. The issue remains unresolved until required checks are complete; apparent success alone does not establish closure. Preserve rationale, authorization, execution evidence, verification result, review decision, and closure status.

**Consistency check:** Complements Decisions 35–37: recovery is separately authorized, emergency authority is bounded, and post-recovery verification/review is required before closure.

**Still open:** Independent-review thresholds, reviewer eligibility/independence, verification evidence, deadlines, and closure/residual-risk criteria.


## 43. Residual-risk acceptance after recovery

**User-selected direction:** C — Risk-based acceptance authority plus explicit record. **WORKING DRAFT — NOT FINAL-LOCKED.**

Policy defines who may accept each level of residual risk. AI explains the risk, evidence, and uncertainty but does not accept risk itself. An authorized person explicitly accepts or rejects and the decision is recorded. Prohibited or unbounded risks cannot be bypassed through acceptance.

**Consistency check:** Complements Decisions 35–38: recovery authority is bounded, every recovery is audited and verified, and residual risk needs separate authorized acceptance.

**Still open:** Risk levels/thresholds, authorized roles, evidence requirements, acceptance expiry/review, and impact on closure.


## 44. Review of accepted residual risk

**User-selected direction:** C — Risk-based review plus change-triggered reassessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

Review timing depends on residual-risk level. Relevant changes to policy, evidence, environment, or assumptions trigger earlier reassessment. Expired or invalid acceptance must not be treated as current approval.

**Consistency check:** Extends Decision 39's explicit, risk-based acceptance and preserves the distinction between accepting residual risk and verifying recovery/closure.

**Still open:** Review intervals, material-change triggers, expiry semantics, reviewer, notifications/escalation, and closure impact.


## 45. Risk increases during reassessment

**User-selected direction:** C — Risk-based revalidation plus temporary safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

A material increase can make existing acceptance insufficient. Apply proportionate temporary safeguards, pausing or containing affected consequential actions when needed; verify the evidence and route for authorized reassessment. A signal alone is not conclusive proof.

**Consistency check:** Builds on Decisions 39–40: explicit risk acceptance is reviewed over time and is not automatically permanent when relevant conditions change.

**Still open:** Materiality thresholds, safeguard catalogue, continuation authority, evidence standards, and in-flight handling.


## 46. System behavior while risk reassessment is pending

**User-selected direction:** C — Targeted hold on affected actions. **WORKING DRAFT — NOT FINAL-LOCKED.**

Potentially affected consequential actions are proportionately paused or restricted. Unaffected work may continue only when demonstrably safe and independent of the unresolved risk. Uncertainty, scope, and restrictions remain visible.

**Consistency check:** Aligns with Decisions 41–42's risk-based revalidation approach while avoiding both blind continuation and unnecessary whole-project freezes.

**Still open:** Affected-scope determination, safe-continuation criteria, hold ownership, status labels, escalation timing, and dependencies on held work.


## 47. Authority to lift a risk hold

**User-selected direction:** C — Risk-based authorized release plus verified prerequisites. **WORKING DRAFT — NOT FINAL-LOCKED.**

Policy defines who can release holds at each risk level. Required evidence and safety prerequisites must be verified first. AI alone cannot release a hold on consequential actions, and approval cannot bypass prohibited risks.

**Consistency check:** Extends Decisions 41–42 on reassessment and targeted holds, while keeping release as a governed, auditable decision.

**Still open:** Risk-to-role mapping, release evidence, independence for high-risk cases, emergency release rules, and audit details.


## 48. Verification after risk-hold release

**User-selected direction:** C — Scope-specific pre-resume verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Before resuming, revalidate current state, authority, applicable policy, evidence, and prerequisites for affected actions and dependencies. Resume only the verified scope; unknown state remains on hold. Release approval alone is not proof that execution is safe.

**Consistency check:** Complements Decision 43 by separating authorization to release a hold from verification that a particular action can safely resume.

**Still open:** Checks by action/risk tier, authoritative evidence sources, dependency boundaries, freshness, and partial/in-flight work.


## 49. Verification fails after risk-hold release

**User-selected direction:** C — Restore hold on affected scope plus bounded diagnosis. **WORKING DRAFT — NOT FINAL-LOCKED.**

Actions with failed or unknown required checks remain on hold. Only policy-authorized, bounded diagnosis is allowed; blind retries and unsafe rollback are not. Verified unaffected work may continue if safe and independent.

**Consistency check:** Complements Decisions 43–44: hold release requires verified prerequisites, and failed verification prevents resumption of the affected scope.

**Still open:** Diagnostic bounds/permissions, retry criteria, containment, dependency scope, escalation, and re-release after remediation.


## 50. Escalation when diagnosis cannot resolve the issue

**User-selected direction:** C — Risk-based escalation plus bounded investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Investigation remains within policy-defined time, budget, and evidence limits. Unresolved issues escalate according to risk and required authority. The affected scope remains held while unresolved; handoff is not resolution.

**Consistency check:** Extends Decisions 45–46 by preventing indefinite autonomous diagnosis while preserving safeguards and risk-appropriate human authority.

**Still open:** Risk-to-escalation mapping, investigation limits, recipients, response deadlines, unavailable-authority handling, and notifications/status rules.


## 51. Escalation recipient unavailable

**User-selected direction:** C — Pre-authorized escalation chain plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

Use policy-defined alternate authorities and response deadlines. If no valid alternate is available, keep the affected scope held and continue only permitted monitoring. Show escalation status; recipient unavailability does not grant AI additional authority.

**Consistency check:** Extends Decision 46's risk-based escalation and preserves Decision 26/36's rule that delegated/emergency authority must be explicitly pre-authorized.

**Still open:** Alternate roles, deadlines, availability detection, monitoring limits, channels, and prolonged-unavailability handling.


## 52. Escalation deadline missed

**User-selected direction:** C — Deadline-based escalation ladder plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

After a policy-defined response deadline expires without a response, advance only along the authorized escalation ladder. Record attempts and delivery/acknowledgement status. If no valid authority responds, keep the affected scope held and mark escalation overdue. Silence is not approval or resolution.

**Consistency check:** Extends Decisions 46–47 without allowing a missed deadline or repeated notification to expand AI authority or release a hold.

**Still open:** Risk-specific deadlines, ladder ownership, cadence, delivery-failure handling, overdue notifications, and prolonged non-response handling.


## 53. All escalation levels exhausted

**User-selected direction:** C — Terminal escalation state plus controlled safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

When all policy-defined escalation levels are exhausted, explicitly record that state and preserve the issue as unresolved. Record the responsible owner and next review condition where defined by policy. Keep the affected scope held; continue only permitted monitoring. Hold release requires a valid authorized decision and required verification.

**Consistency check:** Extends Decisions 46–48; escalation exhaustion, silence, or elapsed time cannot count as resolution or grant additional AI authority.

**Still open:** Owner assignment, review triggers/cadence, terminal-state labels, visibility/notifications, and monitoring scope.


## 54. Review after all escalation levels are exhausted

**User-selected direction:** C — Risk-based review plus meaningful-change triggers. **WORKING DRAFT — NOT FINAL-LOCKED.**

Review cadence follows risk, with earlier reassessment triggered by material changes to relevant evidence, policy, authority availability, or issue state. A review trigger does not mean resolution or release the hold.

**Consistency check:** Extends Decisions 49–50 while preserving the rule that reassessment, evidence changes, or a scheduled review cannot independently authorize consequential work.

**Still open:** Risk-to-review intervals, material-change definitions, trigger sources, ownership, missed-review handling, notifications, and resource limits.


## 55. Conflicting evidence during review

**User-selected direction:** C — Preserve conflict plus bounded verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Preserve conflicting evidence, timestamps, and provenance. Verify only through approved authoritative sources within defined bounds. If conflict remains unresolved, mark the result uncertain and keep affected scope held. Neither recency nor a review trigger independently establishes truth or authorizes hold release.

**Consistency check:** Aligns with Decisions 16–18 and 50; uncertainty is explicit, evidence is not silently discarded, and consequential work remains held until required authorization and verification.

**Still open:** Source precedence, verification limits, freshness, conflict-resolution ownership, and status/notification rules.


## 56. Decision authority when conflicting evidence remains unresolved

**User-selected direction:** C — Risk-based authorized review plus explicit uncertainty. **WORKING DRAFT — NOT FINAL-LOCKED.**

AI explains the evidence conflict and possible impact. A reviewer authorized by policy determines whether evidence is sufficient, more verification is required, or uncertainty may be accepted. AI alone does not resolve consequential status. The affected scope remains held unless all applicable release conditions are met.

**Consistency check:** Extends Decisions 16–18 and 51; preserves conflicting evidence and uncertainty, while separating AI analysis from authorized disposition.

**Still open:** Risk-based reviewer roles, evidence sufficiency criteria, permissible uncertainty acceptance, independence, expiry/review, and hold-release implications.


## 57. Authorized reviewer cannot resolve uncertainty

**User-selected direction:** C — Explicit unresolved disposition plus future review. **WORKING DRAFT — NOT FINAL-LOCKED.**

When an authorized reviewer cannot resolve uncertainty, preserve the evidence gaps and record the issue as unresolved. Maintain applicable holds and safeguards. Reassess on a risk-based cadence or when relevant new evidence arrives; reassessment alone does not release the hold.

**Consistency check:** Extends Decisions 50–52; uncertainty remains explicit, and neither reviewer pressure nor a review trigger substitutes for evidence, authorization, or required verification.

**Still open:** State labels, review cadence, evidence-change triggers, ownership, notification rules, and residual-uncertainty acceptance criteria.


## 58. Accepting unresolved uncertainty to proceed

**User-selected direction:** C — Explicit risk acceptance plus bounded safe continuation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Unresolved uncertainty may be accepted only where applicable policy permits and an authorized person explicitly accepts documented residual risk. Record uncertainty, evidence gaps, accepted scope, rationale, and validity conditions. Continue only demonstrably safe work independent of the unresolved issue. Prohibited actions remain blocked, and AI cannot accept risk itself. Acceptance does not bypass separate authorization or verification requirements.

**Consistency check:** Extends Decisions 50–53 without turning a review or risk-acceptance record into automatic hold release.

**Still open:** Policy eligibility, risk-to-authority mapping, expiry/review triggers, safe-independence criteria, and evidence requirements.


## 59. Accepted residual risk changes later

**User-selected direction:** C — Change-triggered reassessment plus targeted safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

Relevant material changes in evidence, policy, environment, or assumptions trigger revalidation of previously accepted residual risk. Apply proportionate safeguards and restrict or hold affected consequential actions when needed. Only demonstrably safe work independent of the affected risk may continue. Prior acceptance is not automatically sufficient after a material change.

**Consistency check:** Extends Decisions 40–55: change-triggered review does not itself resolve uncertainty, accept new risk, or release a hold.

**Still open:** Material-change criteria, risk thresholds, safeguard catalogue, reassessment authority, evidence freshness, and in-flight handling.


## 60. Choosing temporary safeguards during risk reassessment

**User-selected direction:** C — Pre-approved safeguard policy plus escalation. **WORKING DRAFT — NOT FINAL-LOCKED.**

The AI may identify or apply only predefined safeguards explicitly authorized by policy. If policy does not cover the situation or the case is ambiguous, hold affected consequential work and request authorized review. Safeguards do not resolve the underlying risk or independently release a hold.

**Consistency check:** Extends Decisions 41–55; temporary controls remain bounded by policy and do not replace risk acceptance, authorization, or verification.

**Still open:** Safeguard catalogue, applicability criteria, activation authority, ambiguity thresholds, escalation timing, and in-flight handling.


## 61. Verifying temporary safeguard effectiveness

**User-selected direction:** C — Independent, risk-based verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Verify actual safeguard effectiveness using policy-defined authoritative evidence and checks proportionate to risk. A successful apply/command response alone is not proof. If verification is unavailable, conflicting, or uncertain, the safeguard remains unverified, affected consequential work stays on hold, and authorized review/escalation is required.

**Consistency check:** Extends Decisions 56–57; applying a safeguard and verifying its effect are distinct, and neither automatically resolves the underlying risk or releases a hold.

**Still open:** Authoritative sources, verification depth, evidence freshness, independence requirements, and escalation timing.


## 62. Previously verified safeguard becomes ineffective

**User-selected direction:** C — Revoke verified status, targeted hold, and authorized response. **WORKING DRAFT — NOT FINAL-LOCKED.**

If evidence indicates a safeguard is no longer effective, its prior verification remains historical evidence only and must not imply current effectiveness. Update current status to ineffective/unverified, hold or restrict affected consequential work, and use only policy-authorized containment. Replacement or recovery requires authorized review, and any replacement safeguard must be independently verified.

**Consistency check:** Extends Decisions 56–58; history is retained without silently preserving a stale current status, and replacement is not trusted without verification.

**Still open:** Failure-detection evidence, status labels, containment catalogue, response authority, replacement approval, and in-flight handling.


## 63. Monitoring safeguard failure

**User-selected direction:** C — Risk-based monitoring plus change-triggered checks. **WORKING DRAFT — NOT FINAL-LOCKED.**

Monitoring strength should reflect risk and potential failure impact. Use scheduled checks, available event/change signals, and re-verification on relevant changes. Monitoring gaps, stale evidence, and coverage limitations must be visible. Do not silently treat a safeguard as currently verified when coverage or evidence is uncertain.

**Consistency check:** Extends Decisions 56–59; monitoring complements independent verification and does not imply continuous coverage or guarantee detection.

**Still open:** Risk-to-monitoring mapping, intervals, event signals, freshness thresholds, gap alerts, and behavior when monitoring is unavailable.


## 64. Safeguard monitoring becomes unavailable

**User-selected direction:** C — Monitoring-degraded state plus risk-based restrictions. **WORKING DRAFT — NOT FINAL-LOCKED.**

When monitoring is unavailable, explicitly show the coverage gap and uncertainty about current effectiveness. Apply risk-proportionate restrictions or holds to affected consequential actions. Alternative verification is permitted only when policy explicitly authorizes it. Once monitoring returns, reconcile evidence and revalidate current status.

**Consistency check:** Extends Decisions 56–60; monitoring outage is neither evidence of safeguard failure nor proof of continued effectiveness, and restoration alone does not verify status.

**Still open:** Degraded-state labels, restriction mapping, approved alternatives, outage thresholds, reconciliation, and alert/escalation rules.


## 65. Evidence gaps after monitoring restoration

**User-selected direction:** C — Reconcile available evidence plus explicit gap assessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

After monitoring returns, reconcile available authoritative evidence and document the outage interval, missing evidence, and remaining uncertainty. Perform risk-based revalidation; monitoring restoration alone does not restore verified status. If a material gap remains unresolved, maintain applicable holds/restrictions on affected consequential work.

**Consistency check:** Extends Decisions 59–61; restoration is not proof of historical coverage or current safeguard effectiveness.

**Still open:** Gap materiality thresholds, authoritative sources, reconstruction limits, risk-to-revalidation mapping, and release criteria.

## 66. Evidence-gap materiality criteria (Decision 62 — working draft)
Use policy-defined criteria that consider affected-work risk, the duration/extent of the missing interval, the criticality of missing evidence, and plausible impact if work proceeds. AI can explain the evidence and implications but cannot create or override materiality rules. If criteria are missing, ambiguous, or do not cover the case, affected consequential work remains held/restricted until authorized review. Preserve evidence provenance, uncertainty, criteria/version used, assessment, and disposition. Monitoring restoration by itself does not prove the gap immaterial or restore verified status.

## 67. Ownership and versioning of materiality criteria (Decision 63 — working draft)
A designated authorized policy owner approves and versions materiality criteria. Each change must record rationale, effective date, version, and assessed impact; preserve prior versions and change history. Assess gaps against the applicable approved version. AI may explain but cannot invent or override policy. If ownership, applicability, or criteria are ambiguous, keep affected consequential work held/restricted pending authorized review.

## 68. Existing assessments after criteria changes (Decision 64 — working draft)
A materiality-criteria change triggers impact-based reassessment. Identify potentially affected assessments and reassess those against the currently applicable approved version. Preserve historical results and the criteria/version history. Apply proportionate holds or restrictions to affected consequential work while impact remains unresolved or required reassessment is incomplete. Do not assume prior validity persists automatically, and do not indiscriminately rerun unrelated assessments without an identified impact basis.

## 69. Status during criteria reassessment (Decision 65 — working draft)
Preserve the previous verified result as history while showing current status as `REASSESSMENT PENDING` or equivalent explicit uncertainty until required reassessment completes. Apply proportionate restrictions to affected consequential work. Update current status using the applicable approved criteria version and evidence after reassessment. Do not represent historical verification as current, or automatically label every affected assessment INVALID solely because criteria changed.

## 70. Overdue reassessment handling (Decision 66 — working draft)
Apply policy-defined risk-based reassessment deadlines. When a deadline is missed, preserve pending status and applicable restrictions and trigger authorized review/escalation. Missing a deadline is not approval, resolution, acceptance of uncertainty, or automatic restoration of the prior status. Retain deadline and escalation history and the eventual disposition.

## 71. Escalation exhausted without response (Decision 67 — working draft)
When policy-defined escalation levels and attempts are exhausted without response, retain a persistent unresolved state and applicable restrictions. Only explicitly permitted monitoring and safe independent work may continue. Further consequential action requires an authorized decision and required verification. Preserve escalation history and later disposition; silence or escalation exhaustion is not approval or resolution.

## 72. Review of persistently unresolved issues (Decision 68 — working draft)
Apply risk-based periodic review and change-triggered earlier review for relevant new evidence, policy changes, authority availability, or issue-state changes. Preserve the trigger, evidence/state considered, outcome, and remaining uncertainty. A review trigger or completed review does not automatically lift restrictions or authorize consequential work; applicable release conditions remain in force.

## 73. Issue remains unresolved after review (Decision 69 — working draft)
Record an explicit unresolved disposition, remaining evidence gaps, checks attempted, and open questions. Maintain applicable holds/restrictions and schedule the next review or escalation under policy. Repeated unsuccessful reviews do not automatically close the issue; closure requires an authorized decision and required verification.

## 74. Risk-based scheduling of the next review (Decision 70 — working draft)
Policy defines default review deadlines by risk level. An authorized reviewer may approve an earlier or later date with a documented reason only where policy permits. Overdue review is not closure, approval, or grounds to automatically lift restrictions.

## 75. Missed review deadline (Decision 71 — working draft)
Maintain visible overdue status and trigger risk-based escalation as policy requires. If escalation is unavailable, keep applicable holds/restrictions active. A missed deadline does not constitute approval, resolution, or permission to release a hold.

## 76. Response from an authorized reviewer after escalation (Decision 72 — working draft)
Validate reviewer authority and decision scope; preserve the response, rationale, and unresolved uncertainty. Required evidence and verification must be complete before relevant status or restrictions change. Response receipt alone does not release a hold or resolve the issue.

## 77. Unclear or incomplete reviewer decision (Decision 73 — working draft)
Use `AMBIGUOUS / INCOMPLETE`, record missing or conflicting details, and request authorized clarification. Applicable holds/restrictions remain active. Do not infer missing authorization or intent from an ambiguous response.

## 78. No response to clarification request (Decision 74 — working draft)
Use policy-bounded follow-ups, preserve delivery/status history, and escalate through an authorized route when the limit is reached. No response leaves ambiguity and applicable restrictions in place; silence is not approval.

## 79. Escalation exhausted without clarification (Decision 75 — working draft)
Set `ESCALATION EXHAUSTED — UNRESOLVED`, preserve the attempt/response history, maintain applicable restrictions, and set the next review under policy. Exhaustion does not authorize approval, closure, or hold release.

## 80. Ownership after escalation is exhausted (Decision 76 — working draft)
Preserve the existing authorized owner. Reassign only through formal assignment to an alternate authorized under policy. If none exists, mark `OWNER UNAVAILABLE`, maintain applicable restrictions, and preserve review status. AI must not transfer ownership or authority independently.

## 81. Process after `OWNER UNAVAILABLE` (Decision 77 — working draft)
Use policy-defined timing and authorized escalation. Assign only via a pre-authorized alternate or formally authorized leadership decision. Otherwise preserve `OWNER UNAVAILABLE` and applicable restrictions, and schedule review per policy. Silence or notification is not authority transfer.

## 82. Undefined escalation timeframe (Decision 78 — working draft)
Missing policy timing is a policy gap. Request authorized clarification or policy update; record `ESCALATION TIMING UNDEFINED`, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. Do not invent deadlines or authority.

## 83. Review timing when a policy gap remains unresolved (Decision 79 — working draft)
Use applicable policy or formally authorized reviewer direction only where permitted. If no valid schedule exists, record `REVIEW TIMING UNDEFINED`, request clarification, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. Do not invent deadlines.

## 84. No response to policy clarification request (Decision 80 — working draft)
Use only policy-defined follow-ups, deadlines, and escalation routes. If undefined, record the gap and request direction from an authorized policy owner. Maintain undefined status and applicable restrictions until valid direction arrives; no AI-generated fallback policy.

## 85. Policy owner also unavailable (Decision 81 — working draft)
Use only a policy-defined authorized alternate governance contact or decision path. If none exists, record `POLICY GOVERNANCE UNAVAILABLE`, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. AI must not appoint authority or independently resolve the policy gap.

## 86. Review trigger when governance is unavailable (Decision 82 — working draft)
Use policy-defined triggers and timing only. If undefined, record `GOVERNANCE REVIEW TRIGGER UNDEFINED` and request authorized clarification. New information does not automatically change authority or restrictions; AI must not create review schedules or governance authority.

## 87. New critical information during a governance gap (Decision 83 — working draft)
Preserve information/evidence, assess urgency against approved criteria, and apply only policy-authorized protective measures. If criteria or authority are unclear, record `CRITICAL INFORMATION — GOVERNANCE REVIEW REQUIRED`, maintain restrictions, and allow only explicitly permitted safe containment/monitoring. AI must not expand authority independently.

## 88. Critical information notification during a governance gap (Decision 84 — working draft)
Use policy-authorized recipients and approved channels only. If either is undefined/unavailable, record `CRITICAL NOTIFICATION PATH UNAVAILABLE`, track delivery accurately, and maintain restrictions. AI must not invent recipients, channels, or escalation authority.

## 89. Failed critical notification delivery (Decision 85 — working draft)
Use policy-authorized retry limits and approved fallback channels only. Record failure/uncertainty. If no authorized route exists, set `CRITICAL NOTIFICATION DELIVERY UNRESOLVED`, maintain restrictions, and continue only permitted safe monitoring/containment. AI must not independently switch channels or invent escalation authority.

## 90. Delivered notification without acknowledgement (Decision 86 — working draft)
Keep delivery and acknowledgement separate. Follow policy-defined deadlines, reminder limits, and escalation routes. A missed deadline follows policy; silence is not approval or resolution. If rules are undefined, record the gap, maintain restrictions, and do not invent deadlines or authority.

## 91. Acknowledgement without issue resolution (Decision 87 — working draft)
Treat acknowledgement as a communication event, not issue resolution. Maintain issue status independently; acknowledgement cannot close the issue, release restrictions, grant approval, or transfer ownership. Follow-up uses applicable policy and authorized workflow only.

## 92. Closure verification fails or is unavailable (Decision 88 — working draft)
Closure requires policy-defined verification. If verification fails, conflicts, or is unavailable, record `CLOSURE UNVERIFIED`, preserve the evidence gap, and maintain restrictions. Only policy-authorized review/recovery is allowed; AI cannot invent closure criteria.

## 93. Contradictory evidence after closure verification (Decision 89 — working draft)
Preserve evidence, source, and timestamp; assess relevance/reliability under applicable policy. If impact is unclear, set `CLOSURE UNDER REVIEW` and maintain necessary restrictions. Closure decisions require the authorized policy-defined process; AI cannot independently reverse or confirm closure.

## 94. Closure after review of contradictory evidence (Decision 90 — working draft)
Preserve contradiction-resolution evidence and reviewer disposition. Closure requires applicable policy criteria and verification, including independent verification where required. Missing, conflicting, or unverifiable criteria retain `CLOSURE UNVERIFIED` and applicable restrictions. Reviewer confirmation alone does not replace evidence.

## 95. Incomplete or unavailable audit record after closure (Decision 91 — working draft)
Record audit gaps and preserve available records/provenance. Set `CLOSURE AUDIT REVIEW REQUIRED`; assess closure validity under applicable policy and maintain necessary restrictions. AI must not fabricate entries or independently reopen/re-close the issue.

## 96. Audit record repaired but completeness unverified (Decision 92 — working draft)
Record repair provenance, method, and confidence/verification status; distinguish original from reconstructed evidence. If completeness is unverified, set `AUDIT COMPLETENESS UNVERIFIED` and maintain restrictions. Change review status only under policy-defined criteria and authorized review; AI cannot invent missing history.

## 97. Clearing audit review after completeness verification (Decision 93 — working draft)
Preserve verification evidence/provenance. Clear review status only after policy criteria are met and authorized review approves. Validate closure separately; completeness alone does not validate closure. Missing criteria or uncertainty means review status and applicable restrictions persist.

## 98. Audit review cleared but issue closure validation fails (Decision 94 — working draft)
Keep audit-review clearance separate from issue-closure validity. Record `ISSUE CLOSURE VALIDATION FAILED` or `ISSUE CLOSURE VALIDATION UNAVAILABLE` as applicable, preserve evidence, and maintain restrictions. Closure disposition follows authorized policy-defined process; audit clearance alone does not close or reopen the issue.

## 99. Reviewer unavailable after closure validation fails (Decision 95 — working draft)
Keep closure validation unresolved and use only policy-designated alternate reviewers or explicitly authorized governance routes. If unavailable, record `CLOSURE REVIEWER UNAVAILABLE`, maintain restrictions, and allow only permitted safe monitoring/containment. AI cannot appoint reviewers, reverse closure, or transfer authority independently.

## 100. Resuming review when the authorized reviewer returns (Decision 96 — working draft)
Revalidate reviewer identity, authority, scope, latest issue status, closure evidence, audit trail, policy, and intervening changes. Reuse only verified prior work. Stale, missing, or conflicting evidence remains unresolved. Resume only within validated scope; restrictions persist while required checks are incomplete.

## 101. Missing or conflicting evidence when review resumes (Decision 97 — working draft)
Record evidence gaps/conflicts with provenance and timestamps; use bounded verification from policy-approved authoritative sources. Affected closure determination remains unresolved with restrictions maintained. Unaffected work may continue only if demonstrably safe and independent. Unresolved gaps follow authorized policy-defined review/escalation; AI does not independently select evidence to settle conflicts.

## 102. Evidence conflict unresolved after authorized review (Decision 98 — working draft)
Record `EVIDENCE CONFLICT UNRESOLVED` and preserve evidence, provenance, attempted checks, and uncertainty. Maintain applicable restrictions. Residual-risk acceptance or bounded safe continuation is possible only where policy permits and authority explicitly approves; otherwise hold affected consequential work. Review/change triggers must be policy-defined. Review completion alone is not closure or hold release.

## 103. Residual-risk acceptance expires or becomes invalid (Decision 99 — working draft)
Expired/invalid acceptance is not current authorization. Fresh assessment/acceptance follows applicable policy and authorized review. Maintain restrictions on affected consequential work; safe independent work only where explicitly permitted. Preserve previous acceptance, expiry/invalidity reason, current evidence, and reassessment history. Expiry alone is not closure or risk acceptance.

## 104. Restrictions after fresh residual-risk acceptance (Decision 100 — working draft)
Validate fresh acceptance and recheck current evidence, authority, safeguards, and policy-required checks. Lift only restrictions whose release prerequisites pass and policy permits. Missing, conflicting, or uncertain checks preserve affected restrictions. Approval alone is not proof of release eligibility.

## 105. Resuming work after restrictions are lifted (Decision 101 — working draft)
Revalidate current state, action status, authority, approval validity, policy, and dependencies. Resume only verified, authorized work in released scope. Material changes need fresh approval; unknown state stays paused. Verify and record each resumed action's outcome.

## 106. Resumed action failure or unknown outcome (Decision 102 — working draft)
Reconcile state using approved bounded read-only checks. Unknown is neither success nor failure. Prohibit blind retry/rollback/repair; pause affected and dependent work while allowing only demonstrably safe independent work. Retry/recovery requires explicit policy authorization and verified prerequisites.

## 107. Confirmed action failure after reconciliation (Decision 103 — working draft)
Record failure and assess affected workflow. Retry requires explicit policy permission, duplicate-side-effect risk assessment, verified current state/prerequisites, and valid required authorization. Otherwise pause and present recovery options/evidence to the user or authorized reviewer. Failure confirmation alone is not retry authorization.

## 108. Retry limits for eligible failed actions (Decision 104 — working draft)
Retry count, timing, and backoff require approved action-specific policy. Missing configuration, unclear retry safety, or uncertain outcome means no automatic retry in the MVP; pause and ask. Record each attempt. Exhausted limits route to authorized recovery.

## 109. First MVP implementation target (Decision 105 — working draft)
Use Context Note Update as the first end-to-end action. Gate execution on authority, applicable policy/risk/approval checks, and required durable audit. Require expected-version conditional write, preserve prior version, and verify read-back content/version before context/timeline update. Unknown outcomes require bounded approved read-only reconciliation; no blind retry/rollback/repair. The illustrative ₹0-to-₹500 example is not real-action authorization.

## Project Bible v1.0 Development Baseline
The Project Bible is approved as the development baseline for implementation. This does not close any unresolved policy, authority, evidence, audit, or production-readiness gaps. Where a required rule or authority is undefined, the existing conservative hold/clarification behavior remains in force; implementation must not invent policy or authority to fill a gap.

