# 19 — IMPLEMENTATION READINESS REVIEW

**Status: WORKING DRAFT — NOT FINAL-LOCKED.** This review translates the four recorded Approval & Action Safety decisions into proposed acceptance criteria and identifies implementation dependencies. It does not approve a technology stack, physical schema, API endpoints, risk thresholds, or new product scope.

## 1. Executive assessment

**Assessment: Policy direction is coherent; implementation readiness is partial.**

The four working decisions fit the existing conceptual architecture and Controlled Action flow. No need for a new top-level service, new MVP feature, or technology commitment is established by this review.

However, the project is **not ready to implement the complete safety lifecycle end-to-end** until the team defines the minimum contracts and implementation choices listed below. Some independent work—such as tests, interface sketches, and provider-neutral policy logic—can proceed without choosing the final stack, provided it does not create an unapproved contract.

## 2. Architecture consistency

### Consistent with existing documents
- `08_architecture.md` already places the permission check before action execution, followed by result verification and context/timeline update.
- `05_requirements.md`, `06_user_flows.md`, and `07_feature_specs.md` already require `AUTO / ASK / BLOCK`, bounded AUTO behavior, informed approval, result verification, action history, and no automatic retries.
- `09_database.md` already identifies conceptual needs for action history, approval status, execution details, verification results, and failure/recovery details without approving a physical schema.
- `10_api_contract.md` explicitly prohibits inventing endpoints and requires frontend/backend agreement before implementation.
- `13_definition_of_done.md` already requires tests, error handling, contract compliance, and documentation updates.

### Alignment needed
The four decisions add specificity to Controlled Action. Keep the same conceptual architecture and update its requirement/flow documentation to state:
1. Hybrid governance: system safety limits; authorized project administrators configure AUTO permissions within those limits.
2. Four working risk levels: `LOW`, `MODERATE`, `HIGH`, `CRITICAL`; exact criteria and thresholds remain undecided.
3. Risk-tiered verification and hold-on-uncertainty.
4. Unknown outcomes are reconciled before further action; no automatic retry, rollback, or repair.
5. Pre-execution evidence baseline; approval bound to exact reviewed action/version, target, scope, and material parameters.
6. Required audit record must be durably saved before dispatch; revalidate after delays or material changes.

### No architecture change justified yet
Do not add a new microservice, database, agent, framework, or vendor solely to satisfy these policy rules. Place responsibilities in existing application modules once the implementation architecture is approved.

## 3. Proposed acceptance criteria for review

These are **proposed, testable criteria** derived from the working decisions. They are not final acceptance criteria until explicitly approved.

### AC-01 — Permission configuration respects safety limits
Given an authorized project administrator configures an AUTO permission, when the requested permission exceeds a mandatory system safety limit, the configuration is rejected and the reason is recorded.

### AC-02 — Unauthorized configuration is rejected
Given a principal without the required project-administrator permission attempts to change AUTO configuration or an overrideable BLOCK, when the request is evaluated, the change is rejected without changing effective policy.

### AC-03 — Risk is recorded before dispatch
Given an action is proposed, when the system evaluates it for execution, a risk classification, rationale, and supporting evidence reference are recorded before dispatch. Exact thresholds remain a separate decision.

### AC-04 — Risk-tiered verification is enforced
Given an action type has an approved risk level and verification policy, when it is executed, the system requires the verification evidence defined for that level before reporting the action as verified.

### AC-05 — Uncertainty holds execution
Given risk, authority, required evidence, or a required verification method is materially uncertain, when the system cannot resolve the uncertainty, it does not dispatch the affected consequential action and explains the missing evidence.

### AC-06 — Approval is bound to the reviewed request
Given ASK approval is obtained for a specific action/version, when the target, scope, or material parameters change, the prior approval is not silently reused; the request is revalidated and fresh approval is obtained when required.

### AC-07 — Audit persistence gates dispatch
Given required approval/risk/action records cannot be durably persisted, when execution is about to start, dispatch does not occur. If storage later recovers, current policy, risk, authority, approval, request version, and execution status are revalidated first.

### AC-08 — Unknown outcome is not treated as failure
Given a request times out after dispatch and the external outcome cannot be determined, the system records `UNKNOWN / UNVERIFIED`, preserves the last verified state, and does not automatically retry, roll back, or repair.

### AC-09 — Reconciliation precedes recovery
Given an action outcome is unknown, when reconciliation evidence is available, the system uses bounded, authorized checks before proposing a retry, rollback, repair, or resume. If state remains unknown, affected work stays paused.

### AC-10 — Verified outcome updates context
Given authoritative evidence establishes the actual outcome, when context or timeline is updated, only claims supported by that evidence are marked verified; the earlier unknown state and correction/history link remain available.

### AC-11 — Multi-step actions preserve partial state
Given one step in a multi-step action fails or becomes uncertain, the system records completed, failed, in-progress, and unknown steps separately; dependent steps pause. Independent steps may continue only if explicitly defined as safe and still authorized.

### AC-12 — Audit history is attributable
Given a risk assessment, approval, execution, verification, correction, or recovery event occurs, the record identifies the relevant request/action and preserves the event history without silently overwriting earlier records. Exact fields and retention rules remain undecided.

## 4. Genuine implementation blockers and dependencies

| Dependency | Why it blocks affected implementation | Minimum next decision |
|---|---|---|
| Approved action/tool contract | The system cannot safely validate targets, parameters, scope, dependencies, and side effects without a defined action representation | Define the first supported action type and its inputs, effects, and success condition |
| Identity and role/permission model | “Authorized project administrator” must be verifiable, not inferred | Define the minimum identity/role contract and authorization check location |
| Risk criteria and action mapping | Four labels alone do not determine which actions are LOW/MODERATE/HIGH/CRITICAL | Define criteria and initial mapping for the first supported action types |
| Verification contract | Each enabled action needs a success condition and an authoritative evidence source | Specify verification for each initial action type |
| Durable audit persistence | The pre-dispatch gate depends on a clear definition of successful durable persistence and recovery behavior | Choose the storage approach and its durability/failure semantics as part of the approved architecture |
| Approval request/version contract | Approval must bind to the exact reviewed action and material parameters | Define canonical request/version representation and material-change rules |
| Unknown-outcome reconciliation | Recovery behavior depends on what status evidence each integration exposes | Define permitted read-only reconciliation method for each initial integration |
| API and data contracts | Frontend, backend, policy logic, and integrations need matching shapes | Approve contracts before implementation; do not invent endpoints in this review |

These are blockers for the affected end-to-end safety behavior, not reasons to pause all project work. Provider-neutral tests, threat scenarios, interface prototypes, and contract proposals can be drafted while the stack is undecided.

## 5. Decisions deliberately left open

- Exact numeric risk thresholds and classification rules.
- Which action types are eligible for AUTO, ASK, or BLOCK.
- Permanent versus overrideable BLOCK categories and override authority.
- Approval expiry, revocation, and time validity rules.
- Exact evidence fields, storage technology, retention, deletion, and legal-hold mechanics.
- Material-change definition and canonical request/version format.
- Action-specific verification sources and independent-check requirements.
- Reconciliation bounds, unresolved-state ownership, and integrations with no reliable status API.
- Frameworks, database, API endpoints, authentication provider, deployment, and model/provider versions.
- Reviews 1008–1012 remain unanswered; they are not approved.

## 6. Suggested implementation sequence

1. Approve a minimal action contract for one low-risk, reversible, observable action.
2. Define identity/role checks, policy evaluation inputs, and the exact approval binding for that action.
3. Define its risk classification rationale and authoritative verification method.
4. Define the durable audit-write boundary and behavior when persistence fails.
5. Define reconciliation behavior for timeout/unknown outcome before allowing any retry or recovery.
6. Write automated tests for AC-01 through AC-12 as applicable to the first action type.
7. Only then expand the supported action catalogue, applying the same gates to each new action type.

## 8. First action contract — Context Note Update v0.1

**Conflict handling selected by the user:** A — Stop and ask. **WORKING DRAFT — NOT FINAL-LOCKED.**

If the note changes after the agent prepares an update, pause the update, show the current and proposed versions, and ask the user how to proceed. Do not silently overwrite the newer edit, automatically rebase, or merge. After the user decides, revalidate authorization, policy, request version, and required evidence before continuing.

Proposed test cases:
- **CN-01:** If the note version is unchanged and all gates pass, apply only the approved update and verify by reading back the saved note.
- **CN-02:** If the note version changed after preparation, pause and show both versions; no write occurs until the user decides.
- **CN-03:** If the user chooses to proceed after a conflict, revalidate the request against the latest note and current safety/authorization checks.
- **CN-04:** If the conflict decision is absent or ambiguous, remain paused.
- **CN-05:** If the outcome is unknown after dispatch, record `UNKNOWN / UNVERIFIED`; reconcile before any recovery and do not automatically retry or roll back.
- **CN-06:** Preserve the old and new versions, conflict event, user decision, and verified result in the action history, subject to the still-undecided concrete data/retention design.

**Still open:** Exact interface, allowed resolution choices, version/concurrency mechanism, risk mapping, durable storage semantics, and detailed API/schema. The example budget-note change is illustrative only.

## 9. Pre-write concurrency rule — Working Decision 6

**User-selected direction:** A — Atomic version check. **WORKING DRAFT — NOT FINAL-LOCKED.**

The system must compare the current note version to the exact approved version and perform the comparison and write atomically. A mismatch rejects the write, preserves the newer content, pauses the action, and invokes the stop-and-ask conflict flow from Working Decision 5. A non-atomic fallback is not permitted. If storage cannot guarantee this behavior, implementation of this path is blocked until a safe mechanism is selected.

Additional proposed acceptance tests:
- **CN-07:** If the current version matches the approved version, the conditional write may proceed only if all other current safety and authorization gates pass.
- **CN-08:** If the current version differs, the write is rejected and the newer content remains intact.
- **CN-09:** Simulate a concurrent edit between preparation and write; no newer edit is overwritten.
- **CN-10:** If atomic conditional write is unavailable, the system holds the operation rather than using check-then-write.
- **CN-11:** After conflict resolution, a new write requires current authorization/policy/evidence checks and approval binding appropriate to the revised request.
- **CN-12:** Record expected version, conflict outcome, user resolution, and verified result in attributable history; exact fields remain undecided.

**Still open:** Storage technology, version token, conditional-write API, revised request approval flow, idempotency/lost-ack handling, and concrete audit schema.

## 10. Save-timeout handling — Working Decision 7

**User-selected direction:** A — Reconcile before recovery. **WORKING DRAFT — NOT FINAL-LOCKED.**

A timeout is not proof of success or failure. Mark the outcome `UNKNOWN / UNVERIFIED`; perform authorized, bounded reconciliation; record success only when authoritative evidence verifies the intended content/version. If state is unresolved, pause and ask. Never automatically retry, roll back, or repair. Revalidate current authorization, policy, expected version, approval binding, and evidence before a newly authorized write.

Additional proposed acceptance tests:
- **CN-13:** A simulated timeout marks the action `UNKNOWN / UNVERIFIED`, not success or failure.
- **CN-14:** If authoritative readback verifies the intended content/version, record the verified outcome and retain the timeout/reconciliation history.
- **CN-15:** If the old version remains, do not automatically retry; pause and enter the applicable recovery/approval flow.
- **CN-16:** If readback is unavailable or inconclusive, keep the outcome unknown, explain known/unknown state, and ask the user how to proceed.
- **CN-17:** No automatic retry, rollback, or repair occurs after a timeout.
- **CN-18:** Before any newly authorized write, revalidate current authorization, policy, expected version, approval binding, and required evidence.
- **CN-19:** Reconciliation uses only authorized, bounded read methods and preserves the last verified state.

**Still open:** Timeout thresholds, reconciliation bounds/source, authoritative state criteria, idempotency/request identifiers, lost-ack handling, unresolved-state ownership/escalation, and concrete audit fields.

## 11. Duplicate-write prevention — Working Decision 8

**User-selected direction:** A — Unique Operation ID plus reconciliation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Assign a unique Operation ID to each logical update. After a timeout or lost acknowledgement, reconcile the original operation; do not automatically create a new operation or retry. If status cannot be established, pause and ask. An ID alone does not guarantee exactly-once execution.

Additional proposed acceptance tests:
- **CN-20:** Each logical update has a unique Operation ID associated with the original request and audit history.
- **CN-21:** After timeout/lost acknowledgement, the system checks the original operation rather than automatically creating a new one.
- **CN-22:** No automatic retry occurs after timeout/lost acknowledgement.
- **CN-23:** If original-operation status cannot be established through authorized bounded reconciliation, keep the outcome unknown, pause, and explain the uncertainty.
- **CN-24:** A verified original result is recorded without erasing timeout/reconciliation history.
- **CN-25:** Before a separately authorized new write, revalidate current authorization, policy, expected version, approval binding, and required evidence.
- **CN-26:** Tests and documentation do not treat the presence of an Operation ID as proof of exactly-once execution.

**Still open:** ID format/lifecycle, durable association before dispatch, authoritative operation-status source, retention/lookup window, idempotency guarantees of the storage mechanism, and process-restart/lost-ack handling.

## 12. Unknown-outcome escalation — Working Decision 9

**User-selected direction:** A — Bounded authorized reconciliation, then inform user and remain paused if status is unknown. **WORKING DRAFT — NOT FINAL-LOCKED.**

If bounded authorized reconciliation cannot establish the original operation's result, preserve `UNKNOWN / UNVERIFIED`, pause, explain known/unknown state to the user, and ask how to proceed. No automatic retry, new operation, rollback, or repair. Revalidate all applicable authorization/policy/version/approval/evidence gates before later authorized writes or consequential recovery.

Additional proposed acceptance tests:
- **CN-27:** Reconciliation uses only authorized sources/methods and stops at a defined bound.
- **CN-28:** If status remains unknown at the bound, affected work stays paused and the user is informed of known/unknown state.
- **CN-29:** Unresolved status does not trigger automatic retry, new operation, rollback, or repair.
- **CN-30:** Later authorized writes/recovery revalidate current authorization, policy, expected version, approval binding, and required evidence.
- **CN-31:** Timeout, reconciliation attempts, notification, and later findings remain in attributable history.

**Still open:** Concrete bounds, authoritative status source, notification channel and acknowledgement semantics, escalation recipient/ownership if user is unavailable, and unresolved-case expiry.

## 13. Status

- Four user-selected safety decisions: recorded as working decisions.
- Architecture consistency: no new architectural component is currently justified.
- Acceptance criteria in this document: proposed for review, not final-approved.
- Implementation blockers: action contract, role/identity contract, initial risk mapping, verification source, durable audit semantics, approval version binding, and reconciliation support.
- Final-lock: **not requested and not performed**.

## 12. User unavailable while outcome remains unknown — Working Decision 10

**User-selected direction:** Pause and wait for the user. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, no automatic further work, retry, rollback, repair, or recovery occurs while the outcome is unknown and the user is unavailable. Preserve the last verified state and operation/reconciliation history; on the user's return, explain known/unknown state and ask for direction.

Additional proposed acceptance tests:
- **CN-27:** If the outcome remains unknown and the user is unavailable, the action remains paused.
- **CN-28:** While paused under this condition, no automatic retry, rollback, repair, or recovery is initiated.
- **CN-29:** The original operation identity, last verified state, timeout, reconciliation evidence, and uncertainty history remain preserved.
- **CN-30:** When the user returns, the system explains known/unknown state and requires direction before further action.
- **CN-31:** Any later action revalidates applicable authorization, policy, expected version, approval binding, and required evidence.

**Still open:** Availability detection, notification behavior, paused-work retention/ownership, and escalation behavior outside this specific case.

## 13. Resume revalidation after user returns — Working Decision 11

**User-selected direction:** Revalidate all relevant current state. **WORKING DRAFT — NOT FINAL-LOCKED.**

When the user returns and asks to continue a paused Context Note Update v0.1 operation, revalidate original operation status, current note state/version, authorization, applicable policy, approval validity/binding, and required evidence. Remain paused if uncertainty persists. A request to continue does not prove outcome or authorize a repeated write by itself.

Additional proposed acceptance tests:
- **CN-32:** Resume request triggers checks of original operation status and current note state/version.
- **CN-33:** Resume request triggers current authorization, policy, approval validity/binding, and required-evidence checks.
- **CN-34:** If any required outcome/state/authority/approval/evidence remains uncertain, the action stays paused and the uncertainty is explained.
- **CN-35:** A resume request does not automatically create a new operation, retry, rollback, repair, or recovery.
- **CN-36:** Any later write/recovery is gated by current applicable controls.

**Still open:** Authoritative status/version sources, automated check boundaries, approval expiry/revocation rules, and user-facing revalidation result presentation.

## 14. Intended note update already applied — Working Decision 12

**User-selected direction:** Verify, record, and stop. **WORKING DRAFT — NOT FINAL-LOCKED.**

If the intended Context Note Update v0.1 result may already be saved, verify current note content/version against the intended result using authoritative evidence. If verified, record the original operation's verified outcome, preserve timeout/reconciliation history, and do not write again. If evidence is incomplete/ambiguous or content/version differs, remain paused and explain known/unknown state.

Additional proposed acceptance tests:
- **CN-37:** The system verifies current note content and version against the intended result before marking the original operation verified.
- **CN-38:** When the intended result is verified, the system records the original operation outcome and preserves timeout/reconciliation history.
- **CN-39:** The system does not issue a second write or replacement operation for the same intended update after verifying it is already applied.
- **CN-40:** If content/version differs or evidence is incomplete/ambiguous, the system does not assume success and remains paused.
- **CN-41:** A separate follow-up change is treated as a distinct operation and revalidated against applicable controls.

**Still open:** Authoritative content/version source, exact comparison semantics, and evidence threshold for verified outcome.

## 15. Content matches but version/operation metadata differs — Working Decision 13

**User-selected direction:** Content match alone is insufficient; require content and version verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

If content appears to match but expected version or operation metadata differs, keep the action paused, reconcile authoritative current state and available original-operation evidence using bounded authorized checks, and preserve the discrepancy/history. If unresolved, retain `UNKNOWN / UNVERIFIED` and ask the user. No automatic write, replacement operation, retry, rollback, or repair.

Additional proposed acceptance tests:
- **CN-42:** Matching content alone does not verify the original operation when expected version or operation metadata differs.
- **CN-43:** The discrepancy triggers authorized, bounded reconciliation and the action remains paused during investigation.
- **CN-44:** The discrepancy, timeout, reconciliation attempts, and subsequent findings remain in history.
- **CN-45:** If the discrepancy cannot be authoritatively explained, the outcome remains `UNKNOWN / UNVERIFIED` and the user is informed.
- **CN-46:** No automatic write, replacement operation, retry, rollback, or repair occurs under this condition.

**Still open:** Authoritative metadata source, version-change interpretation, reconciliation sources, and evidence threshold for explaining a discrepancy.

## 16. User-facing uncertainty when evidence is unavailable — Working Decision 14

**User-selected direction:** Provide a transparent uncertainty report. **WORKING DRAFT — NOT FINAL-LOCKED.**

When authoritative evidence is unavailable, report the intended action, observed current state, missing/unavailable verification evidence, and actions not taken. Keep `UNKNOWN / UNVERIFIED` if evidence is insufficient, preserve history, and keep the action paused pending user direction.

Additional proposed acceptance tests:
- **CN-47:** The report states the intended action.
- **CN-48:** Observed current state is clearly distinguished from unverified state.
- **CN-49:** Missing/unavailable evidence or verification source is identified without overstating conclusions.
- **CN-50:** The report states that no automatic new write, retry, rollback, repair, or recovery was taken.
- **CN-51:** Insufficient evidence leaves the operation `UNKNOWN / UNVERIFIED` and paused.
- **CN-52:** Original operation, timeout, observations, reconciliation attempts, and uncertainty history are preserved.

**Still open:** Exact report format, evidence-source naming, sensitive-data redaction, and user-authorized further investigation.

## 17. User-authorized investigation boundaries — Working Decision 15

**User-selected direction:** A — Bounded read-only investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-53:** Investigation requires explicit user authorization and is restricted to bounded, read-only inspection of permitted evidence sources.
- **CN-54:** The original operation ID, timeout, observations, reconciliation attempts, evidence, and uncertainty history are preserved.
- **CN-55:** Findings distinguish verified facts from incomplete or ambiguous evidence.
- **CN-56:** Investigation does not write or modify the note, create a replacement operation, retry, rollback, repair, or perform recovery.
- **CN-57:** If the outcome cannot be resolved within the investigation boundary, the state remains `UNKNOWN / UNVERIFIED` and the action remains paused.
- **CN-58:** Any consequential follow-up requires separate authorization and applicable policy, risk, authority, approval, and verification checks.

**Still open:** Permitted evidence sources, investigation step/time/resource limits, source-authority criteria, and reporting format.

## 18. Evidence source authority and trust — Working Decision 16

**User-selected direction:** A — Pre-approved authoritative sources only. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-59:** Investigation consults only pre-approved authoritative sources relevant to the original operation.
- **CN-60:** Unavailability of an approved source does not automatically promote an alternative source to authoritative status.
- **CN-61:** Unavailable, stale, incomplete, or conflicting approved evidence leaves the outcome `UNKNOWN / UNVERIFIED` unless required verification is established from an approved source.
- **CN-62:** Source observations and evidence/reconciliation history remain traceable.
- **CN-63:** Evidence investigation remains bounded and read-only and does not trigger a write, replacement operation, retry, rollback, repair, or recovery.

**Still open:** Actual approved source list, source approval/versioning, freshness rules, conflict-handling rules, and source-specific evidence thresholds.

## 19. Conflicting authoritative evidence — Working Decision 17

**User-selected direction:** A — Preserve conflict and pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-64:** Both conflicting pre-approved source observations and available timestamps/relevant provenance are preserved.
- **CN-65:** The system does not silently prefer one source or resolve conflict by assumption.
- **CN-66:** If conflict prevents required verification, outcome remains `UNKNOWN / UNVERIFIED`.
- **CN-67:** The affected action remains paused and the user receives a clear explanation of the conflict and unresolved conclusion.
- **CN-68:** Reconciliation remains bounded, read-only, and restricted to pre-approved authoritative sources; no write, retry, replacement operation, rollback, repair, or recovery occurs as part of reconciliation.

**Still open:** Exact evidence fields, timestamp/provenance representation, and whether source-specific precedence rules may be approved for narrowly defined future cases.

## 20. User notification on evidence conflict — Working Decision 18

**User-selected direction:** A — Notify immediately; suppress duplicate alerts; update on meaningful status changes. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-69:** User is notified when conflicting authoritative evidence blocks required verification and the affected action is paused.
- **CN-70:** Duplicate alerts for the same unresolved conflict are suppressed when there is no meaningful change.
- **CN-71:** Meaningful evidence/status changes trigger an update.
- **CN-72:** The notification distinguishes known facts from unresolved conclusions, states that the action is paused, and minimizes sensitive information.
- **CN-73:** Outcome remains `UNKNOWN / UNVERIFIED` while required verification is unresolved.
- **CN-74:** Notification and status-change history is preserved for traceability.

**Still open:** Notification channel, delivery/acknowledgement semantics, meaningful-change criteria, failed-notification retry/escalation, and redaction rules.

## 21. Notification delivery failure — Working Decision 19

**User-selected direction:** A — Record failure, use only explicitly permitted bounded retries, then preserve failure/unknown delivery status and keep the action paused. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-75:** Notification attempts and observed delivery results are recorded accurately.
- **CN-76:** Retries occur only under explicitly permitted bounded rules and do not continue indefinitely.
- **CN-77:** Unconfirmed delivery is not reported as delivered or received without supporting evidence.
- **CN-78:** Notification delivery status is separate from the underlying operation/action status.
- **CN-79:** The affected action remains paused while its underlying outcome is unresolved.
- **CN-80:** Notification attempts, delivery evidence, status changes, and retry history are preserved.

**Still open:** Retry count/backoff/time limits, authoritative delivery evidence, failure-versus-unknown classification, fallback channels, and escalation if the user remains unreachable.


## 22. Notification delivery evidence — Working Decision 20

**User-selected direction:** C — Keep request/provider acceptance, delivery confirmation, and user read/acknowledgement distinct. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-81:** Sending-system request acceptance is not represented as provider acceptance or delivery.
- **CN-82:** Provider acceptance is recorded only with supporting provider evidence and is not represented as delivery confirmation.
- **CN-83:** Delivery is marked confirmed only when approved, channel-appropriate evidence supports it.
- **CN-84:** Read/acknowledgement is separate from delivery and is recorded only when explicitly supported.
- **CN-85:** An unestablished lifecycle stage remains `UNKNOWN / UNVERIFIED`; the UI does not overstate certainty.
- **CN-86:** Notification lifecycle status is separate from the original operation/action outcome.
- **CN-87:** Status transitions and supporting evidence are preserved for traceability.

**Still open:** Approved provider/channel evidence sources, delivery receipt semantics, read/acknowledgement definitions, evidence freshness/retention, duplicate or out-of-order status events, and user-facing wording.


## 23. Undeliverable notification fallback — Working Decision 21

**User-selected direction:** A — Pause and wait as the baseline. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-88:** Notification attempts and supported results are recorded accurately; unconfirmed delivery is not represented as confirmed.
- **CN-89:** After permitted bounded attempts, failure or `UNKNOWN / UNVERIFIED` delivery status and attempt history are preserved.
- **CN-90:** No fallback channel, contact, or escalation route is automatically selected unless separately authorized under applicable policy.
- **CN-91:** The affected action remains paused while its underlying operation outcome remains unresolved; notification status does not determine operation status.
- **CN-92:** When the user returns, the pending issue is surfaced with known facts, unknown delivery state, and unresolved operation state distinguished.
- **CN-93:** Any future fallback or escalation is subject to explicit authorization and applicable policy, permission, privacy, and bounded-attempt checks.

**Still open:** User availability detection, pending-issue surfacing, retention/ownership while waiting, and whether pre-authorized fallback channels or risk-based escalation should be considered later.


## 24. Surfacing unresolved issues when the user returns — Working Decision 22

**User-selected direction:** C — Persistent pending-issues panel plus return alert. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-94:** An unresolved notification-delivery issue triggers a prominent alert on a qualifying user return/session start.
- **CN-95:** The issue remains visible in a persistent pending-issues panel until resolved or explicitly dismissed under applicable rules.
- **CN-96:** Dismissing the alert does not resolve the issue or change delivery/operation status.
- **CN-97:** User return or alert dismissal alone never resumes a paused action or supplies authorization for a new action.
- **CN-98:** UI presentation distinguishes known facts, unknown/unverified delivery state, and unresolved operation outcome.

**Still open:** Session/return detection contract, alert frequency/deduplication, panel ordering, dismissal authority/semantics, retention/ownership.


## 25. Pending issue acknowledgement — Working Decision 23

**User-selected direction:** B — Acknowledge, but keep unresolved. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-99:** User acknowledgement is recorded as a distinct event.
- **CN-100:** Acknowledgement does not change delivery status, operation status, verification state, or issue resolution.
- **CN-101:** The issue remains unresolved and visible in the persistent panel after acknowledgement.
- **CN-102:** Acknowledgement alone does not resume a paused action or authorize retry/recovery.
- **CN-103:** Resolution and consequential follow-up still require applicable evidence, authorization, and verification.

**Still open:** Acknowledgement reversal, display, shared-project authority, exact audit fields.


## 26. Shared-project acknowledgement permissions — Working Decision 24

**User-selected direction:** C — Any project member may acknowledge; resolution remains separately restricted. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-104:** An authorized project member can acknowledge a pending issue as seen.
- **CN-105:** The acknowledging actor and event are recorded separately from resolution and operation/delivery status.
- **CN-106:** Acknowledgement alone cannot resolve/verify an issue or authorize resumption, retry, recovery, or consequential follow-up.
- **CN-107:** Resolution and consequential actions enforce separate authorization, evidence, and verification requirements.

**Still open:** Membership/guest boundaries, actor audit fields, resolution role model.


## 27. Conflicting shared-project instructions — Working Decision 25

**User-selected direction:** C — Role-based authority with pause on ambiguity. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-108:** Authorization is evaluated against the actor's current role and permission scope.
- **CN-109:** Conflicting, overlapping, or ambiguous authority causes the affected action to pause and request authorized clarification.
- **CN-110:** One project member's instruction does not automatically override another's instruction or an existing approval.
- **CN-111:** Acknowledgement, resolution, and action-execution permissions remain distinct.
- **CN-112:** Mandatory safety, evidence, approval, and verification requirements cannot be bypassed by role authority.
- **CN-113:** Before resumption, revalidate current authority and relevant action state.

**Still open:** Role hierarchy, escalation path, conflict criteria, queued/in-flight handling, pending-approval effects.


## 28. Owner unavailable during instruction conflict — Working Decision 26

**User-selected direction:** C — Use a delegate if authorized; otherwise pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-114:** A delegate can resolve a conflict only when explicit, current authority covers the specific action.
- **CN-115:** Delegate action remains within the authorized scope.
- **CN-116:** If no suitable delegate exists or authority/scope is ambiguous, the affected action remains paused pending authorized clarification.
- **CN-117:** Owner unavailability, seniority, or project membership alone does not transfer or create authority.
- **CN-118:** Conflict, authority basis, pause, clarification, and resolution history are preserved, and current state is revalidated before resumption.

**Still open:** Delegate designation/revocation, scope, expiry, unavailability criteria, escalation route, queued/in-flight effects.


## 29. Delegate authority expiry/revocation — Working Decision 27

**User-selected direction:** C — State-aware authority revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-119:** New actions depending on an expired/revoked delegation are blocked.
- **CN-120:** Pending affected actions pause and require current authority revalidation before resumption.
- **CN-121:** In-flight actions are assessed using actual state; the system does not blindly cancel, retry, or roll back.
- **CN-122:** Unknown outcomes remain `UNKNOWN / UNVERIFIED` unless approved bounded reconciliation establishes sufficient authoritative evidence.
- **CN-123:** Consequential follow-up applies fresh-authorization requirements where applicable.
- **CN-124:** Delegation changes, action state, evidence, and authorization history are preserved; queued work is revalidated before dispatch.

**Still open:** Revocation propagation timing, authoritative delegation source, in-flight classes/safe-stop, reauthorization requirements, queued work.


## 30. Previously approved action after delegate revocation — Working Decision 28

**User-selected direction:** C — Revalidate approval binding and current authority. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-125:** Before dispatch, the system revalidates approver identity, authority basis, approval scope/version, current policy, target, and action state.
- **CN-126:** Revocation alone is not treated as proof that an earlier approval is either valid or invalid.
- **CN-127:** Ambiguous approval validity or authority keeps the action paused and requires fresh authorization.
- **CN-128:** Original approval, revocation event, revalidation evidence, and resulting decision are preserved.
- **CN-129:** No action dispatches until applicable current authority and approval checks pass.

**Still open:** Default effect of revocation on delegated approvals, approval versioning, queued approvals.


## 31. Scope or parameter changes after approval — Working Decision 29

**User-selected direction:** C — Strict binding with explicit materiality rules. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-130:** Approval is bound to the exact reviewed action, target, version, scope, and relevant parameters.
- **CN-131:** Proposed changes are compared with the approved request before dispatch.
- **CN-132:** Non-material changes proceed only when covered by explicit approved policy rules and all other checks pass.
- **CN-133:** Material changes trigger revalidation and fresh approval where applicable.
- **CN-134:** Ambiguous materiality or approval applicability causes a pause and clarification, not silent approval reuse.
- **CN-135:** Original/changed requests, comparison evidence, classification, and approval history are preserved.

**Still open:** Action-specific materiality criteria, criteria authority, always-material changes, detection/versioning.


## 32. Material-change classification — Working Decision 30

**User-selected direction:** C — Policy rules plus AI assistance; ambiguity means pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-136:** Materiality is classified against approved policy rules.
- **CN-137:** AI can identify and explain differences but cannot create unsupported exceptions or override policy.
- **CN-138:** Uncovered or ambiguous changes pause and require clarification.
- **CN-139:** The system preserves compared versions, differences, applied rule/version, AI assistance, classification, and approval/revalidation history.
- **CN-140:** A change is not dispatched on the basis of an unsupported AI-only classification.

**Still open:** Rule ownership/approval, versioning, escalation for uncovered cases, and recording of AI suggestions.


## 33. Approval validity and expiry — Working Decision 31

**User-selected direction:** C — Risk-based expiry plus pre-execution revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-141:** Approval validity is determined using approved risk-based policy rules.
- **CN-142:** Before execution, current authority, approved action/scope, relevant policy, and required evidence are revalidated.
- **CN-143:** Expired or ambiguous approval validity causes a pause and fresh-approval workflow.
- **CN-144:** Approval time, validity rule, revalidation evidence, and renewal/fresh-approval history are preserved.
- **CN-145:** The system does not assume approval is indefinitely valid or apply a universal expiry without policy basis.

**Still open:** Risk-to-expiry mapping, invalidating changes, clock source, notification timing, renewal workflow.


## 34. Policy changes after approval — Working Decision 32

**User-selected direction:** C — Revalidate current policy before execution. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-146:** Execution checks the current applicable policy, not just the policy version at approval time.
- **CN-147:** A policy update that prohibits the action or materially changes approval conditions causes a pause and required reapproval workflow.
- **CN-148:** An irrelevant policy update does not force reapproval unless documented rules require it.
- **CN-149:** Approval-time/current policy versions, revalidation result, and pause/reapproval history are preserved.
- **CN-150:** The system does not execute when current policy applicability or required reapproval is ambiguous.

**Still open:** Relevance/materiality criteria, authoritative policy version source, emergency changes, and in-flight policy changes.


## 35. Policy update during an in-flight action — Working Decision 33

**User-selected direction:** C — Risk-aware pause/containment plus state verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-151:** The system assesses current policy applicability when policy changes during execution.
- **CN-152:** If continuation may be prohibited or unsafe, the system attempts an appropriate safe-boundary pause/containment where feasible.
- **CN-153:** The system verifies actual action state before choosing subsequent steps and does not assume cancellation succeeded.
- **CN-154:** The system does not blindly retry, roll back, or continue when policy impact or state is unresolved.
- **CN-155:** Policy changes, observed state, containment/verification steps, and review decisions are preserved.

**Still open:** Action-specific safe boundaries, urgent handling, continuation/recovery authority, and partial-action verification sources.


## 36. Action cannot be safely paused — Working Decision 34

**User-selected direction:** C — Action-specific safe-stop policy plus containment. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-156:** The system consults action-specific safe-stop rules instead of assuming a universal pause/interrupt behavior.
- **CN-157:** If safe interruption is unavailable, the system avoids unapproved new steps and uses only permitted containment.
- **CN-158:** The system monitors/verifies actual state and does not treat completion alone as proof of safety or compliance.
- **CN-159:** Applicable rules, policy changes, containment steps, observed state, verification evidence, and review history are preserved.
- **CN-160:** Unresolved risk or state uncertainty leads to authorized review rather than unsupported continuation or recovery.

**Still open:** Safe-stop catalogue, containment options, verification sources, escalation timing, and post-event recovery authority.


## 37. Recovery authorization after safe-stop — Working Decision 35

**User-selected direction:** C — Risk- and role-based fresh authorization. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-161:** Recovery is represented as a separate action from the original operation.
- **CN-162:** Original approval alone cannot authorize consequential recovery.
- **CN-163:** Before recovery, actual state, current authority, risk, and applicable policy are validated.
- **CN-164:** Consequential recovery requires fresh approval from an appropriately authorized person.
- **CN-165:** Emergency permissions are used only when explicitly defined by policy and within scope.
- **CN-166:** Recovery rationale, evidence, authorization, execution, and verification history are preserved.

**Still open:** Recovery risk tiers, eligible roles, emergency boundaries, approval binding/expiry, and recovery-specific verification requirements.


## 38. Recovery approver unavailable — Working Decision 36

**User-selected direction:** C — Pre-authorized escalation path. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-167:** The system checks for an explicitly authorized alternate/emergency role for the recovery scope.
- **CN-168:** Approver unavailability alone never grants authority to AI or another person.
- **CN-169:** Alternate authority, policy, scope, and action state are revalidated before recovery.
- **CN-170:** Without a valid authorized path, the system safely holds where possible, monitors, and escalates without consequential recovery.
- **CN-171:** Availability status, escalation path, authority checks, evidence, and recovery decision history are preserved.

**Still open:** Eligible roles, scope boundaries, escalation timing, availability detection, and permitted monitoring/containment.


## 39. Emergency recovery authority scope — Working Decision 37

**User-selected direction:** C — Narrow predefined authority plus strict limits. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-172:** Emergency authority is limited to explicitly authorized actions and defined scope/risk limits/expiry.
- **CN-173:** Each recovery action performs required state/evidence checks and creates an auditable record.
- **CN-174:** Exceeding a limit or ambiguous authority blocks execution pending fresh authorization or safe hold.
- **CN-175:** The system does not treat emergency authority as blanket discretionary permission.
- **CN-176:** Authorization, evidence, execution, and post-action review information are preserved.

**Still open:** Role eligibility, action catalogue, risk thresholds, expiry/revocation, evidence requirements, and post-action review.


## 40. Review after emergency recovery — Working Decision 38

**User-selected direction:** C — Audit every recovery plus risk-based independent review. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-177:** Every emergency recovery creates an auditable record.
- **CN-178:** Recovery outcome is verified using required evidence before closure.
- **CN-179:** Higher-risk or exceptional cases require independent human review.
- **CN-180:** The issue remains unresolved until all required checks are complete.
- **CN-181:** Rationale, authorization, execution evidence, verification result, review decision, and closure status are preserved.
- **CN-182:** Apparent success alone cannot close an issue or imply residual risk was accepted.

**Still open:** Review thresholds, reviewer eligibility/independence, evidence requirements, deadlines, and closure/residual-risk criteria.


## 41. Residual-risk acceptance — Working Decision 39

**User-selected direction:** C — Risk-based acceptance authority plus explicit record. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-183:** Current policy maps residual-risk levels to eligible acceptance roles.
- **CN-184:** AI may explain residual risk and evidence but cannot accept risk itself.
- **CN-185:** Acceptance/rejection is explicit and records authority, rationale, scope, and applicable conditions.
- **CN-186:** Prohibited or unbounded risks cannot be bypassed through acceptance.
- **CN-187:** Closure follows the applicable closure criteria; acceptance alone does not silently imply all checks passed.

**Still open:** Risk thresholds, roles, evidence requirements, expiry/review, and closure effects.


## 42. Review of accepted residual risk — Working Decision 40

**User-selected direction:** C — Risk-based review plus change-triggered reassessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-188:** Review cadence is derived from the residual-risk level and applicable policy.
- **CN-189:** Relevant changes to policy, evidence, environment, or assumptions can trigger reassessment before the scheduled review.
- **CN-190:** Expired or invalid acceptance is never represented as current approval.
- **CN-191:** Reassessment outcomes and history are recorded; reassessment does not automatically renew acceptance.

**Still open:** Risk-to-review mapping, trigger materiality, reviewer eligibility, expiry behavior, escalation, and closure effects.


## 43. Risk increases during reassessment — Working Decision 41

**User-selected direction:** C — Risk-based revalidation plus temporary safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-192:** Potential risk increases preserve evidence, provenance, and uncertainty.
- **CN-193:** Policy determines whether the increase is material; a signal alone is not conclusive proof.
- **CN-194:** Material or unresolved consequential risk triggers proportionate safeguards, including pause/containment where needed.
- **CN-195:** Updated risk is routed for authorized reassessment; AI cannot accept the risk.
- **CN-196:** Safeguard and reassessment outcomes are auditable.

**Still open:** Thresholds, safeguard catalogue, continuation authority, evidence standards, and in-flight handling.


## 44. Pending risk reassessment — Working Decision 42

**User-selected direction:** C — Targeted hold on affected actions. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-197:** Potentially affected consequential actions are identified and held/restricted proportionately.
- **CN-198:** Unaffected work continues only if demonstrably safe and independent of the unresolved risk.
- **CN-199:** User-visible status identifies uncertainty, affected scope, and restrictions.
- **CN-200:** Restrictions are not lifted until required authorized reassessment criteria are met.
- **CN-201:** Dependency effects are considered before allowing supposedly unaffected work to continue.

**Still open:** Scope algorithm, safe-continuation criteria, hold owner, status labels, escalation, and dependency handling.


## 45. Authority to lift a risk hold — Working Decision 43

**User-selected direction:** C — Risk-based authorized release plus verified prerequisites. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-202:** Current policy maps risk levels to eligible hold-release authorities.
- **CN-203:** Release prerequisites and required evidence are checked before release.
- **CN-204:** AI alone cannot lift holds on consequential actions.
- **CN-205:** No approval can bypass prohibited risks or mandatory safeguards.
- **CN-206:** Release decision, authority, evidence, scope, timestamp, and conditions are auditable.

**Still open:** Role mapping, evidence standards, independent review for high risk, emergency rules, and audit details.


## 46. Verification after risk-hold release — Working Decision 44

**User-selected direction:** C — Scope-specific pre-resume verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-207:** Release approval is checked for authority and exact scope.
- **CN-208:** Current state, policy, authority, evidence, prerequisites, and dependencies are revalidated before resume.
- **CN-209:** Only scope passing required checks resumes; unknown or failed checks remain held.
- **CN-210:** Release approval is not treated as evidence of safe execution.
- **CN-211:** Checks and outcomes are auditable, including partial/in-flight state.

**Still open:** Action/risk-tier checks, evidence sources, dependency boundaries, freshness, and partial-work handling.


## 47. Failed verification after hold release — Working Decision 45

**User-selected direction:** C — Restore hold on affected scope plus bounded diagnosis. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-212:** Failed or unknown required checks keep the affected scope held.
- **CN-213:** Diagnosis is bounded and explicitly permitted by current policy.
- **CN-214:** Blind retries and unsafe rollback are not performed.
- **CN-215:** Unaffected work continues only if verified safe and independent.
- **CN-216:** Remediation does not itself lift the hold; the authorized release and pre-resume verification flow is repeated.

**Still open:** Diagnostic bounds, retry criteria, containment, dependency scope, escalation, and re-release rules.


## 48. Unresolved diagnosis and escalation — Working Decision 46

**User-selected direction:** C — Risk-based escalation plus bounded investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-217:** Investigation respects configured time, budget, and evidence limits.
- **CN-218:** Unresolved issues escalate to authority appropriate to risk and required decision.
- **CN-219:** Affected scope remains held while unresolved and safeguards remain necessary.
- **CN-220:** Handoff, notification, acknowledgement, or elapsed time is not treated as resolution.
- **CN-221:** Investigation and escalation events, evidence, recipients, and outcomes are auditable.

**Still open:** Risk mapping, limits, recipients, deadlines, unavailable-authority handling, and notification/status rules.


## 49. Escalation recipient unavailable — Working Decision 47

**User-selected direction:** C — Pre-authorized escalation chain plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-222:** Alternate escalation targets are selected only from current policy and verified for required authority.
- **CN-223:** Response deadlines and escalation attempts are recorded.
- **CN-224:** If no valid alternate is available, affected scope stays held and only permitted monitoring continues.
- **CN-225:** Recipient unavailability never silently expands AI authority.
- **CN-226:** Escalation status and delivery/acknowledgement state are visible and auditable.

**Still open:** Alternate roles, deadlines, availability detection, monitoring limits, channels, and prolonged-unavailability handling.


## 50. Missed escalation deadline — Working Decision 48

**User-selected direction:** C — Deadline-based escalation ladder plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-227:** Deadline expiry is evaluated against the applicable policy and recorded status.
- **CN-228:** Escalation advances only to an authorized next recipient.
- **CN-229:** Each attempt and delivery/acknowledgement state is auditable.
- **CN-230:** If no valid authority responds, affected scope remains held and overdue status is visible.
- **CN-231:** Silence, timeout, or repeated notification never counts as approval or resolution.

**Still open:** Risk-specific deadlines, ladder ownership, cadence, delivery-failure handling, overdue notifications, and prolonged non-response handling.


## 51. All escalation levels exhausted — Working Decision 49

**User-selected direction:** C — Terminal escalation state plus controlled safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-232:** The system marks escalation exhausted only after checking the policy-defined escalation chain and recording attempts.
- **CN-233:** The issue remains explicitly unresolved; silence is not approval or closure.
- **CN-234:** Responsible owner and next review condition are recorded when defined by policy.
- **CN-235:** Affected scope remains held; only explicitly permitted monitoring continues.
- **CN-236:** Hold release requires a valid authorized decision and required verification.

**Still open:** Owner assignment, review triggers/cadence, terminal-state labels, visibility/notifications, and monitoring scope.


## 52. Review after escalation is exhausted — Working Decision 50

**User-selected direction:** C — Risk-based review plus meaningful-change triggers. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-237:** Review cadence is selected using the applicable risk policy.
- **CN-238:** Material changes in relevant evidence, policy, authority availability, or issue state can trigger earlier reassessment.
- **CN-239:** Review triggers and review outcomes are recorded with provenance and timestamps.
- **CN-240:** Triggered review does not automatically resolve the issue or release the hold.
- **CN-241:** Hold release still requires the applicable authorized decision and required verification.

**Still open:** Risk intervals, materiality, trigger sources, ownership, missed-review handling, notifications, and resource limits.


## 53. Conflicting evidence during review — Working Decision 51

**User-selected direction:** C — Preserve conflict plus bounded verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-242:** Conflicting evidence items, timestamps, and provenance are preserved.
- **CN-243:** Verification uses only approved authoritative sources and respects configured bounds.
- **CN-244:** Recency alone does not silently determine which evidence is true.
- **CN-245:** Unresolved conflict results in an uncertain status and continued hold on affected scope.
- **CN-246:** Hold release still requires applicable authorized reassessment and required verification.

**Still open:** Source precedence, verification limits, freshness, conflict-resolution ownership, and status/notification rules.


## 54. Authorized disposition of unresolved evidence conflict — Working Decision 52

**User-selected direction:** C — Risk-based authorized review plus explicit uncertainty. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-247:** AI presents conflicting evidence, provenance, limitations, and potential impact without silently choosing a winner.
- **CN-248:** Disposition is routed to a reviewer authorized by the applicable policy.
- **CN-249:** Reviewer records whether evidence is sufficient, further verification is required, or uncertainty may be accepted under policy.
- **CN-250:** AI alone cannot resolve consequential status from unresolved conflict.
- **CN-251:** Hold-release conditions remain separately enforced.

**Still open:** Reviewer roles, evidence sufficiency, uncertainty acceptance, independence, expiry/review, and hold-release implications.


## 55. Reviewer cannot resolve uncertainty — Working Decision 53

**User-selected direction:** C — Explicit unresolved disposition plus future review. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-252:** Evidence gaps and remaining uncertainty are recorded with the reviewer's disposition.
- **CN-253:** The system does not force an unsupported conclusion or mark the issue resolved.
- **CN-254:** Applicable holds and safeguards remain active for the affected scope.
- **CN-255:** Risk-based review or relevant new evidence can trigger reassessment.
- **CN-256:** Reassessment alone cannot release the hold; applicable authorization and verification remain required.

**Still open:** State labels, review cadence, evidence-change triggers, ownership, notifications, and residual-uncertainty acceptance criteria.


## 56. Accepting unresolved uncertainty — Working Decision 54

**User-selected direction:** C — Explicit risk acceptance plus bounded safe continuation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-257:** Residual uncertainty can be accepted only where policy permits and an authorized person explicitly accepts the documented risk.
- **CN-258:** The record includes uncertainty, evidence gaps, accepted scope, rationale, and validity conditions.
- **CN-259:** Only demonstrably safe work independent of the unresolved issue may continue.
- **CN-260:** Prohibited actions remain blocked, and AI cannot accept risk on its own.
- **CN-261:** Risk acceptance does not bypass separate authorization, verification, or hold-release requirements.

**Still open:** Policy eligibility, risk-to-authority mapping, expiry/review triggers, safe-independence criteria, and evidence requirements.


## 57. Accepted residual risk changes later — Working Decision 55

**User-selected direction:** C — Change-triggered reassessment plus targeted safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-262:** Relevant material changes to evidence, policy, environment, or assumptions can trigger risk revalidation.
- **CN-263:** Prior risk acceptance is not automatically treated as sufficient after a material change.
- **CN-264:** Proportionate safeguards are applied and affected consequential actions are restricted or held when required.
- **CN-265:** Only demonstrably safe, independent work continues while reassessment is pending.
- **CN-266:** Change, evidence, reassessment, safeguards, authority, and outcome are preserved in the record.

**Still open:** Material-change criteria, risk thresholds, safeguard catalogue, reassessment authority, evidence freshness, and in-flight handling.


## 58. Temporary safeguards during risk reassessment — Working Decision 56

**User-selected direction:** C — Pre-approved safeguard policy plus escalation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-267:** The AI only identifies/applies predefined safeguards explicitly authorized by policy.
- **CN-268:** Missing policy coverage or ambiguous applicability causes affected consequential work to be held and routed for authorized review.
- **CN-269:** Applying a temporary safeguard does not mark the underlying risk resolved or independently release a hold.
- **CN-270:** Trigger, policy rule, safeguard, scope, and escalation history are recorded.

**Still open:** Safeguard catalogue, applicability criteria, activation authority, ambiguity thresholds, escalation timing, and in-flight handling.


## 59. Temporary safeguard effectiveness verification — Working Decision 57

**User-selected direction:** C — Independent, risk-based verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-271:** Safeguard effectiveness is checked against policy-defined authoritative evidence using risk-appropriate verification.
- **CN-272:** A successful command/apply response alone does not mark a safeguard effective.
- **CN-273:** Missing, conflicting, stale, or uncertain evidence leaves the safeguard unverified.
- **CN-274:** Affected consequential work remains held and authorized review/escalation is requested while verification is unresolved.
- **CN-275:** Safeguard action, evidence, verification result, uncertainty, and follow-up history are preserved.

**Still open:** Authoritative sources, verification depth, evidence freshness, independence requirements, and escalation timing.


## 60. Previously verified safeguard becomes ineffective — Working Decision 58

**User-selected direction:** C — Revoke verified status, targeted hold, and authorized response. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-276:** Credible failure evidence updates current safeguard status to ineffective/unverified without deleting prior verification history.
- **CN-277:** Affected consequential work is held/restricted and only policy-authorized containment is used.
- **CN-278:** Replacement/recovery decisions are routed to authorized review.
- **CN-279:** A replacement safeguard is not treated as effective until independently verified.
- **CN-280:** Failure evidence, status change, containment, authorization, and verification history are preserved.

**Still open:** Detection evidence, status labels, containment catalogue, response authority, replacement approval, and in-flight handling.


## 61. Monitoring safeguard failure — Working Decision 59

**User-selected direction:** C — Risk-based monitoring plus change-triggered checks. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-281:** Monitoring strength/frequency follows an explicit risk and failure-impact policy.
- **CN-282:** Scheduled checks and available event/change signals can trigger re-verification.
- **CN-283:** Monitoring gaps, stale evidence, and coverage limitations are visible.
- **CN-284:** Uncertain current evidence does not silently preserve a verified status.
- **CN-285:** Monitoring observations, evidence, gaps, and follow-up actions are preserved.

**Still open:** Risk mapping, intervals, signal availability, freshness thresholds, gap alerts, and monitoring-unavailable behavior.


## 62. Safeguard monitoring unavailable — Working Decision 60

**User-selected direction:** C — Monitoring-degraded state plus risk-based restrictions. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-286:** Monitoring outage is represented explicitly as degraded coverage with uncertain current effectiveness.
- **CN-287:** Risk-proportionate restrictions/holds apply to affected consequential actions.
- **CN-288:** Alternative verification is used only when policy explicitly authorizes it.
- **CN-289:** Monitoring restoration triggers evidence reconciliation and revalidation, not automatic verified status.
- **CN-290:** Outage periods, evidence gaps, alternative checks, and status transitions are preserved.

**Still open:** State labels, restriction mapping, approved alternatives, outage thresholds, reconciliation, and alert/escalation rules.


## 63. Evidence gaps after monitoring restoration — Working Decision 61

**User-selected direction:** C — Reconcile available evidence plus explicit gap assessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

Acceptance tests:
- **CN-291:** Monitoring restoration does not automatically restore verified status.
- **CN-292:** Available authoritative evidence for the outage interval is reconciled and provenance retained.
- **CN-293:** Missing interval, evidence gaps, and remaining uncertainty are explicitly documented.
- **CN-294:** Risk-based revalidation is performed; unresolved material gaps keep applicable restrictions/holds in place.
- **CN-295:** Reconciliation findings, gap assessment, and status transitions are preserved.

**Still open:** Gap materiality thresholds, authoritative sources, reconstruction limits, revalidation mapping, and release criteria.

## 64. Evidence-gap materiality decision (Decision 62 — working draft)
Implementation requires a policy-owned materiality rule contract covering risk, missing interval/extent, evidence criticality, plausible impact, criteria versioning, and disposition when criteria are absent or ambiguous. AI may explain and organize evidence, not authoritatively classify outside the approved rule. Until those criteria and review ownership are defined, uncovered/ambiguous cases must hold affected consequential work. Acceptance tests CN-296–CN-300 cover policy adherence, non-overriding AI explanation, hold-on-ambiguity, provenance/uncertainty recording, and no automatic status restoration.

## 65. Materiality criteria governance (Decision 63 — working draft)
Implementation requires a designated authorized policy owner and a versioned criteria contract. Persist version, effective date, rationale, assessed impact, and change history, and resolve which approved version applies to each assessment. Unclear ownership/applicability or uncovered criteria must not be silently guessed by AI; affected consequential work remains held/restricted pending authorized review. Acceptance tests CN-301–CN-305 cover owner authorization, version/effective date, rationale/impact, historical traceability, and ambiguity handling.

## 66. Criteria-change impact and reassessment (Decision 64 — working draft)
Implementation needs a criteria-change impact process that maps policy changes to potentially affected assessments, identifies the applicable version, records reassessment results, and preserves historical assessments. Pending impact determination or required reassessment, affected consequential work receives proportionate restrictions. Acceptance tests CN-306–CN-310 cover trigger, current-version application, history retention, pending-work restrictions, and avoiding unsupported blanket invalidation.

## 67. Reassessment-pending status semantics (Decision 65 — working draft)
Implementation needs distinct historical assessment and current-status fields or equivalent event history, with `REASSESSMENT PENDING` (or a defined equivalent) exposed while reassessment is incomplete. Affected consequential work receives proportionate restrictions. Current status updates only after reassessment evaluates evidence under the applicable approved criteria version. Acceptance tests CN-311–CN-315 cover history retention, pending-state visibility, restrictions, evidence-based status update, and no blanket invalidation.

## 68. Overdue reassessment controls (Decision 66 — working draft)
Implementation needs policy-defined risk-to-deadline mapping, overdue detection, authorized escalation routing, persistent pending/restriction state, and audit history for attempts, outcomes, and disposition. Deadline expiry must not auto-approve or restore historical status. Acceptance tests CN-316–CN-320 cover deadline policy, pending status, continued restrictions, escalation records, and no automatic restoration.

## 69. No-response escalation terminal state (Decision 67 — working draft)
Implementation requires policy-defined escalation limits, a persistent unresolved state, retained attempt/delivery/response history, and enforcement of continuing restrictions. Only explicitly permitted monitoring and safe independent work may proceed; consequential action needs authorized decision plus verification. Acceptance tests CN-321–CN-325 cover terminal unresolved state, restrictions, permitted continuation, authorization/verification, and non-approval by silence.

## 70. Unresolved-issue review scheduling and triggers (Decision 68 — working draft)
Implementation needs risk-based periodic scheduling plus event triggers for relevant evidence, policy, authority-availability, and issue-state changes. Persist trigger provenance, review inputs/outcome, and remaining uncertainty. Restrictions must not be cleared solely because a review ran or completed. Acceptance tests CN-326–CN-330 cover periodic schedule, event-triggered review, records, uncertainty retention, and no automatic restriction release.

## 71. Unresolved disposition after review (Decision 69 — working draft)
The implementation must preserve an explicit unresolved status and record evidence gaps, attempted checks, and open questions. Applicable restrictions remain active, with next review/escalation set by policy. Closure must require an authorized decision and required verification. Acceptance tests CN-331–CN-335 cover the disposition, evidence trail, restrictions, review/escalation, and no automatic closure.

## 72. Next-review scheduling for unresolved issues (Decision 70 — working draft)
Implement policy-driven risk-based review deadlines, policy-gated authorized overrides with recorded rationale, and explicit overdue status. Overdue review must not auto-close the issue or restore permissions. Acceptance tests CN-336–CN-339 cover deadline selection, override governance, overdue visibility, and persistent restrictions.

## 73. Overdue review and escalation behavior (Decision 71 — working draft)
Implement persistent overdue status and policy-governed risk-based escalation. When escalation is unavailable, retain applicable restrictions. Acceptance tests CN-340–CN-343 verify visibility, risk-based routing, restriction persistence, and absence of automatic approval/resolution/hold release.

## 74. Reviewer response after escalation (Decision 72 — working draft)
Require authority/scope validation, a recorded disposition and rationale, and completion of required evidence and verification before changing issue status or restrictions. Acceptance tests CN-344–CN-347 cover authority, traceability, verification gates, and prevention of response-only closure or hold release.

## 75. Ambiguous or incomplete reviewer disposition (Decision 73 — working draft)
Require an explicit `AMBIGUOUS / INCOMPLETE` state, a record of missing/conflicting details, and clarification from an authorized reviewer. Restrictions persist while ambiguity remains; AI cannot infer missing authority or intent. Acceptance tests CN-348–CN-351 cover state marking, traceability, authorized clarification, and continued restrictions.

## 76. Clarification request receives no response (Decision 74 — working draft)
Implement policy-defined follow-up limits, attempt/delivery status history, and authorized escalation after the limit. Ambiguity and applicable restrictions persist without a response. Acceptance tests CN-352–CN-355 cover bounded attempts, traceability, escalation, and no approval-by-silence.

## 77. Exhausted escalation without clarification (Decision 75 — working draft)
Implement an explicit `ESCALATION EXHAUSTED — UNRESOLVED` state, traceable escalation attempts and missing response, persistent applicable restrictions, and policy-based next review. Acceptance tests CN-356–CN-359 ensure no automatic approval, closure, or hold release.

## 78. Ownership after escalation is exhausted (Decision 76 — working draft)
Keep owner identity/status traceable; enforce policy-authorized formal reassignment. Without a valid alternate, show `OWNER UNAVAILABLE` while preserving applicable restrictions and review status. Acceptance tests CN-360–CN-363 cover owner traceability, authorized transfer, unavailable-owner state, and no AI-initiated authority transfer.

## 79. `OWNER UNAVAILABLE` handling (Decision 77 — working draft)
Enforce policy-defined timing and escalation, validate the assignment authority path, and preserve unavailable status/restrictions when no valid assignment exists. Notification and silence must not grant authority. Acceptance tests CN-364–CN-367 cover timing, authorized assignment, persistent unavailable status, and no authority transfer through silence.

## 80. Undefined escalation timeframe (Decision 78 — working draft)
Implementation must detect missing policy-defined escalation timing, surface the gap for authorized clarification, preserve the `ESCALATION TIMING UNDEFINED` state and applicable restrictions, and prohibit default/AI-invented deadlines. Acceptance tests CN-368–CN-371 cover gap detection, authorized clarification, restriction persistence, and no invented timing.

## 81. Undefined review timing (Decision 79 — working draft)
Require a valid applicable policy schedule or authorized reviewer direction where policy permits. Otherwise surface `REVIEW TIMING UNDEFINED`, request clarification, preserve restrictions, and prohibit AI-invented deadlines. Acceptance tests CN-372–CN-375 cover valid schedule sources, gap status, restriction persistence, and no invented timing.

## 82. No response to policy clarification request (Decision 80 — working draft)
Enforce policy-defined follow-up and escalation only. When these mechanisms are absent, surface the gap, seek authorized direction, preserve undefined status/restrictions, and prohibit AI-generated fallback policy. Acceptance tests CN-376–CN-379 cover defined routes, missing-route gap, restriction persistence, and no AI fallback.

## 83. Policy governance unavailable (Decision 81 — working draft)
Enforce only policy-defined authorized alternate governance paths. When none exists, surface `POLICY GOVERNANCE UNAVAILABLE`, preserve restrictions, and limit activity to permitted safe monitoring. Acceptance tests CN-380–CN-383 cover authorized alternate validation, unavailable state, restrictions, and no AI-created authority.

## 84. Governance-unavailable review triggers (Decision 82 — working draft)
Enforce policy-defined review triggers/timing. When absent, surface `GOVERNANCE REVIEW TRIGGER UNDEFINED`, request authorized clarification, record relevant new information without automatic authority/restriction changes, and prohibit AI-created schedules/authority. Acceptance tests CN-384–CN-387 cover these controls.

## 85. Critical information during a governance gap (Decision 83 — working draft)
Preserve evidence, use approved urgency criteria, and enforce policy-authorized protective measures only. Where criteria/authority are unclear, surface `CRITICAL INFORMATION — GOVERNANCE REVIEW REQUIRED`, retain restrictions, and limit activity to permitted safe containment/monitoring. Acceptance tests CN-388–CN-391 cover evidence preservation, approved criteria, required status, restrictions, and no authority expansion.

## 86. Critical notification during a governance gap (Decision 84 — working draft)
Enforce authorized recipient and approved-channel constraints. When unavailable/undefined, surface `CRITICAL NOTIFICATION PATH UNAVAILABLE`, track delivery state, preserve restrictions, and prohibit AI-invented recipients/channels/authority. Acceptance tests CN-392–CN-395 cover these controls.

## 87. Failed critical notification delivery (Decision 85 — working draft)
Enforce authorized retry limits and approved fallback channels, track delivery uncertainty, preserve `CRITICAL NOTIFICATION DELIVERY UNRESOLVED` when no valid route exists, and maintain restrictions. Acceptance tests CN-396–CN-399 cover retries, fallback authorization, unresolved state, and no unauthorized switching/authority invention.

## 88. No acknowledgement after delivery (Decision 86 — working draft)
Track delivery separately from acknowledgement, enforce policy-defined timing/reminder/escalation rules, and treat silence as neither approval nor resolution. Where rules are missing, surface the gap and preserve restrictions without AI-invented timing/authority. Acceptance tests CN-400–CN-403 cover these controls.

## 89. Acknowledgement without issue resolution (Decision 87 — working draft)
Keep communication acknowledgement distinct from issue state and prevent it from closing issues, releasing restrictions, granting approval, or transferring ownership. Follow-up must use authorized policy/workflow. Acceptance tests CN-404–CN-407 cover the separation and prohibited side effects.

## 90. Closure verification fails or is unavailable (Decision 88 — working draft)
Require policy-defined closure evidence; failed, conflicting, or unavailable verification keeps the issue in `CLOSURE UNVERIFIED`, with evidence gaps and applicable restrictions preserved. Recovery/review must be authorized, and AI cannot invent criteria. Acceptance tests CN-408–CN-411 cover verification, status, restrictions, and authorization.

## 91. Contradictory evidence after closure verification (Decision 89 — working draft)
Preserve contradictory evidence and provenance, assess it under applicable policy, and retain `CLOSURE UNDER REVIEW` plus necessary restrictions while impact is unclear. Closure decisions remain within authorized policy-defined processes. Acceptance tests CN-412–CN-415 cover provenance, assessment, review status, and prohibition on AI-only closure decisions.

## 92. Closure after review of contradictory evidence (Decision 90 — working draft)
Require traceable evidence resolving the contradiction, recorded reviewer disposition, and applicable policy-defined closure verification. Independent verification is required where policy mandates it. If criteria are missing/conflicting/unverifiable, retain `CLOSURE UNVERIFIED` and restrictions. Acceptance tests CN-416–CN-419 cover evidence, criteria, independent verification, and unresolved status.

## 93. Incomplete or unavailable audit record after closure (Decision 91 — working draft)
Represent missing audit evidence explicitly, preserve available provenance, and trigger `CLOSURE AUDIT REVIEW REQUIRED`. Policy governs closure validity and any further review; necessary restrictions remain. AI cannot fabricate records or decide alone to reopen/re-close. Acceptance tests CN-420–CN-423 cover gaps, provenance, policy review, and prohibited actions.

## 94. Audit record repaired but completeness unverified (Decision 92 — working draft)
Track repair provenance/method/status, separate original evidence from reconstructed data, and retain `AUDIT COMPLETENESS UNVERIFIED` plus applicable restrictions until policy criteria and authorized review permit a status update. AI must not invent missing history. Acceptance tests CN-424–CN-427 cover provenance, distinction, uncertainty, and status controls.

## 95. Clearing audit review after completeness verification (Decision 93 — working draft)
Retain audit-completeness evidence/provenance and require policy criteria plus authorized clearance. Check issue closure independently. Missing criteria or uncertainty keeps review status and restrictions in force. Acceptance tests CN-428–CN-431 cover evidence, authority, separate closure validation, and unresolved status.

## 96. Audit review cleared but issue closure validation fails (Decision 94 — working draft)
Model audit-review clearance and issue-closure validity independently. Record validation failure versus unavailability distinctly, preserve evidence/restrictions, and require authorized policy-defined handling for closure disposition. Acceptance tests CN-432–CN-435 cover independent states, status accuracy, evidence/restrictions, and prevention of automatic closure/reopening.

## 97. Reviewer unavailable after closure validation fails (Decision 95 — working draft)
Preserve unresolved closure-validation status, restrict review to authorized routes, and use `CLOSURE REVIEWER UNAVAILABLE` if no route exists. Maintain applicable restrictions and allow only permitted safe monitoring/containment. Acceptance tests CN-436–CN-439 cover status, alternate authority, restrictions, and prohibited AI actions.

## 98. Resuming review when the authorized reviewer returns (Decision 96 — working draft)
Require revalidation of reviewer identity/authority/scope and current issue/evidence/policy state before resumption. Reuse only still-verified prior work; keep stale, missing, or conflicting evidence unresolved. Acceptance tests CN-440–CN-443 cover authority, current-state rechecks, evidence quality, validated scope, and persistent restrictions.

## 99. Missing or conflicting evidence when review resumes (Decision 97 — working draft)
Require provenance-preserving gap/conflict recording, bounded approved-source verification, unresolved closure status and restrictions, safe-independent-work checks, and authorized escalation. Acceptance tests CN-444–CN-448 cover these requirements and prohibit AI-only evidence selection.

## 100. Evidence conflict unresolved after authorized review (Decision 98 — working draft)
Require explicit unresolved disposition, preserved evidence/provenance/check history, persistent restrictions, and policy-authorized handling of any residual-risk acceptance or safe continuation. Acceptance tests CN-449–CN-453 cover status, evidence preservation, authority, holds, and prevention of automatic closure/hold release.

## 101. Residual-risk acceptance expires or becomes invalid (Decision 99 — working draft)
Require explicit invalid/expired status, policy-governed fresh authorized assessment, targeted restrictions, policy-permitted safe-independent continuation only, and preserved acceptance/evidence history. Acceptance tests CN-454–CN-458 cover authorization validity, reassessment, restrictions, safe continuation, and no automatic closure/risk acceptance.

## 102. Restrictions after fresh residual-risk acceptance (Decision 100 — working draft)
Require validated fresh acceptance, current-state/evidence/authority/safeguard checks, policy-permitted scope-specific release, and persistent restrictions where checks are uncertain. Acceptance tests CN-459–CN-463 cover validation, rechecks, limited release, uncertainty, and prohibition on approval-only release.

## 103. Resuming work after restrictions are lifted (Decision 101 — working draft)
Require pre-resumption revalidation, released-scope enforcement, fresh approval for material changes, pause on unknown state, and verified outcome records. Acceptance tests CN-464–CN-467 cover these requirements.

## 104. Resumed action failure or unknown outcome (Decision 102 — working draft)
Require bounded approved read-only reconciliation, explicit unknown-state handling, no blind retry/rollback/repair, targeted pause, safe-independent-work criteria, and authorized recovery prerequisites. Acceptance tests CN-468–CN-472 cover these requirements.

## 105. Confirmed action failure after reconciliation (Decision 103 — working draft)
Require failure recording and scope assessment; retry gates for policy permission, duplicate-side-effect risk, current state/prerequisites, and valid authorization; otherwise pause with recovery options/evidence. Acceptance tests CN-473–CN-477 cover these controls.

## 106. Retry limits for eligible failed actions (Decision 104 — working draft)
Require approved action-specific retry count/timing/backoff, safe handling when configuration or retry safety is unclear, no automatic retry on uncertain outcomes, per-attempt audit records, and authorized recovery after limit exhaustion. Acceptance tests CN-478–CN-482 cover these controls.

## 107. First MVP implementation target (Decision 105 — working draft)
Start with one Context Note Update and implement/test the full contract: action and expected-version schema; identity/role authorization; AUTO/ASK/BLOCK and risk mapping; approval binding; durable audit gate; conditional write; authoritative read-back verification; timeout/unknown-outcome reconciliation; and verified-only context/timeline update. Do not expand the action catalogue until failure paths are tested. The illustrative ₹0-to-₹500 note is not real-action authorization. Acceptance tests CN-483–CN-487 cover the initial gates.

## Project Bible v1.0 — Development Baseline Approval
**Status: APPROVED AS DEVELOPMENT BASELINE; production readiness not implied.**

The approved implementation starting point is the Context Note Update action using Python + FastAPI. Decisions 1–105 and the documented MVP scope provide the current development baseline. This approval does not close these implementation blockers:
1. Trusted identity and role-authorization contract.
2. Approved action/tool contract and real approval binding, expiry, and revocation.
3. Initial risk-to-action mapping and risk criteria.
4. Action-specific authoritative verification sources.
5. Production-grade durable/protected audit persistence semantics.
6. Unknown-outcome reconciliation and process-crash behavior.
7. Tenant/project isolation, operational limits, monitoring, and security/concurrency validation.

Keep blockers open until their contracts are defined, implemented where applicable, and verified by tests/review. Do not expose the development skeleton publicly or treat demo headers as real authentication/approval.

