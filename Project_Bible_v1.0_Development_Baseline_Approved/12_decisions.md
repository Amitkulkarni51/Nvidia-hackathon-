# 12 — ARCHITECTURE & PRODUCT DECISIONS

## Status
Working decisions from discussion. The recaps were confirmed as accurate, but these decisions have not been final-locked.

Only human-approved final decisions should be treated as authoritative. The entries below preserve the current working direction and must not be described as final-locked.

## Product Direction
- Developer/builder-first Personal AI.
- Initial audience: developers, students building projects, hackathon teams, indie builders, and small technical teams.
- Core differentiator: “Most AI remembers what you decided. Ours remembers why—and knows when that reason stops being true.”
- Product identity and detailed requirements remain working decisions until final approval.

## Decision Memory
- AI drafts a decision record and user reviews/confirms before saving.
- Fields: Decision, Reason, Assumptions, Evidence, Date, Related Project/Goal.

## Decision Health Engine
- States: `HEALTHY`, `AT RISK`, `INVALID`.
- Insufficient evidence should result in `AT RISK` while more evidence is gathered.
- Exact definitions/thresholds remain undecided.

## Assumption Watch and Project State
- Monitor only selected important assumptions linked to decisions.
- AI suggests assumptions; user confirms which to monitor.
- Maintain current project state alongside remembered decisions.

## Impact & Recovery Intelligence
- Explain what changed and evidence, affected decision and why, project impact, alternatives satisfying original constraints, and a recovery plan.

## Controlled Action
- Permission modes: `AUTO / ASK / BLOCK`.
- **Working Decision 1 — AUTO/BLOCK governance:** Hybrid model. The system enforces mandatory safety limits; authorized project administrators can configure AUTO permissions within those limits, subject to role permissions.
- ASK uses informed approval: explain what changed, why action is needed, what will be modified, and expected impact/risks before waiting for approval.
- Mandatory BLOCK restrictions cannot be bypassed by ordinary project permissions. Some other blocks may be overrideable only if policy explicitly permits it, with appropriate authority and informed confirmation.
- Still unresolved: AUTO-eligible action catalogue; permanent versus overrideable BLOCK categories; who may authorize overrides; whether permission changes need additional approval; and how changes affect queued or running actions.
- Verify action execution and intended result; update relevant project state and decision memory from verified outcomes.
- Do not retry failed actions automatically; explain failure/recovery options and wait for approval.
- Keep an action audit trail including attempt, reason/context, permission/approval, execution, verification, and failure/recovery details.

## Decision Timeline
- Separate sixth MVP feature.
- Chronological project timeline of decisions, assumption changes, health changes, actions and approvals, outcomes.
- Automatically record relevant events; users can add personal notes and correct entries.
- Verified events only are presented as verified; uncertain items are marked unverified.
- Show corrected information as current while preserving correction history.
- Chronological default with event-type filters.
- Basic event details: title, date/time, short description.
- Direct deletion with a 30-second undo window; after expiry, undo is unavailable through the normal UI.
- Review 9.7 recap was confirmed accurate, but these remain working decisions and are not final-locked.

## MVP Scope Boundaries
- Current working scope contains six features: Decision Memory, Decision Health Engine, Assumption Watch, Impact & Recovery Intelligence, Controlled Action, Decision Timeline.
- New ideas go to Future Development and are not added to the current MVP.
- Genuine blocker: make only the minimum necessary change.
- Feature replacement requires review of the problem, alternatives, and impact, plus explicit user approval.
- Record reason, approval, and affected features for any approved scope change.
- Current out-of-scope items: email/calendar assistant, giant life-memory system, multiple AI personalities, voice assistant, mobile app, dozens of integrations, custom model training, huge multi-agent system.

## Proposed Technical Roles — Not Finalized
- NVIDIA Nemotron for reasoning.
- Nebius Token Factory for inference/runtime.
- Tavily for finding/verifying changing external information.
- OpenShell/NemoClaw for secure agent actions if suitable.
- Exact model, versions, architecture, and implementation are undecided.

## Unresolved / Do Not Assume
- Product name.
- Exact final state definitions and evidence thresholds.
- AUTO boundaries and how they are configured.
- Which BLOCK categories are permanent vs overrideable.
- Database technology/schema, API endpoints, authentication, deployment, and design system.
- Exact timeline deleted-data retention mechanics.
- Specific measurable success criteria.

## Approval & Action Safety Consolidation
- Consolidated working draft: `docs/17_approval_action_safety.md`.
- This draft groups the conversational Approval & Action Safety reviews into policy domains; it is not final-locked.
- Reviews 1008–1012 remain unanswered and must not be treated as approved.


## Working Decision 2 — Risk levels and verification

**Selected direction:** B + B + A. WORKING DRAFT — NOT FINAL-LOCKED.

- **Risk classification (B):** Use four working levels: `LOW`, `MODERATE`, `HIGH`, `CRITICAL`.
- **Verification (B):** Use risk-tiered verification. Consequential actions require stronger evidence and independent checks where appropriate.
- **Uncertainty (A):** Hold the action when risk or required verification is uncertain or insufficient; gather evidence and reassess. User approval does not bypass mandatory safety limits or unresolved uncertainty.

**Implications**
- Risk classification must consider the actual target, scope, reversibility, potential impact, uncertainty, and current project state—not only the tool/action name.
- Each action type needs a defined success condition and suitable evidence source before it can be safely enabled.
- Verification of execution and verification of the intended outcome are separate requirements.
- The system should preserve the risk classification, evidence, rationale, verification results, and any reassessments in the audit history.

**Open details**
- Exact risk criteria and thresholds for each level.
- Mapping of action types and contexts to risk levels.
- Minimum evidence and independent-verification requirements by risk level.
- What qualifies as an authoritative evidence source and how unavailable verification is handled for each action type.
- Whether any action types are permanently blocked regardless of risk score.

These are working decisions and do not final-lock the policy.


## Working Decision 3 — Unverified action outcome

**Selected direction:** A — Reconcile actual state first. **WORKING DRAFT — NOT FINAL-LOCKED.**

- If an action may have executed but its outcome cannot be verified, mark it `UNKNOWN / UNVERIFIED`; do not report success or failure without evidence.
- Perform bounded, authorized state reconciliation using permitted evidence before retrying, rolling back, repairing, or resuming.
- Do not automatically retry, roll back, or repair.
- Preserve the last verified state and record completed, failed, in-progress, and uncertain steps separately.
- If reconciliation remains inconclusive, stop the affected work, explain what is known and unknown, and offer a recovery proposal.
- Consequential recovery requires applicable authorization/approval and current safety checks. Approval does not bypass mandatory blocks.
- When new evidence resolves the outcome, append a linked finding and update current project/decision state only to the extent supported by verified evidence; preserve the original uncertainty and history.

**Implications**
- A timeout or missing response is not treated as proof of failure.
- The system must distinguish execution status from outcome verification.
- Reconciliation must be bounded and authorized, and must not itself create consequential side effects.
- User-facing status should make uncertainty and the last verified state clear.

**Open details**
- Which read-only reconciliation sources are permitted for each integration/action.
- Reconciliation time/resource bounds and escalation behavior.
- How long an unknown outcome may remain open and who owns follow-up.
- Which recovery actions are consequential and which verification methods are authoritative.
- How to handle integrations that provide no reliable way to inspect actual state.

These are working rules and do not final-lock the policy.


## Working Decision 4 — Minimum pre-execution evidence and audit persistence

**Selected direction:** B + A + A. **WORKING DRAFT — NOT FINAL-LOCKED.**

- **Minimum evidence (B):** Use a risk-tiered baseline. Before execution, require the exact action; authority/permission evidence; safety assessment; verification plan; and a durably saved audit record. Require stronger evidence for higher-risk actions.
- **Approval binding (A):** Bind approval to the exact reviewed action/version, target, scope, and material parameters. Material changes require revalidation and fresh approval where applicable; approval cannot be silently reused for a materially changed request.
- **Audit persistence failure (A):** Stop execution until required records are durably saved and current safety/authorization checks remain valid.

**Implications**
- Approval, evidence sufficiency, audit persistence, execution, and outcome verification are distinct states and must not be conflated.
- A saved audit record proves that the required record was persisted; it does not prove the action executed successfully.
- The system needs a reliable pre-dispatch gate that confirms required records are durable before dispatch.
- If persistence is restored after a delay, the system must revalidate risk, policy, authority, approval validity, action version, and execution status before dispatching.
- Higher-risk actions need stronger evidence and, where appropriate, independent verification or additional authorization.

**Open details**
- Exact evidence fields and storage durability guarantees.
- What counts as a material action/version/parameter change.
- Approval expiry, revocation, and revalidation rules.
- Risk-specific evidence requirements and authoritative verification sources.
- Recovery behavior if a record write succeeds but its acknowledgement is lost, or if execution status becomes uncertain.
- Whether a preapproved fallback audit store may be permitted in a future implementation.

These are working decisions and do not final-lock the policy.

## Working Decision 5 — Context Note Update v0.1 conflict handling

**Selected direction:** A — Stop and ask. **WORKING DRAFT — NOT FINAL-LOCKED.**

When a project-context note changes after the agent has prepared an update but before the update is applied:
- Pause the pending update.
- Show the current version and the proposed version, making the difference clear.
- Ask the user how to proceed.
- Do not silently overwrite the newer edit, automatically rebase, or merge.
- Resume only after the user makes a decision and the action is revalidated against current authorization, policy, request version, and required evidence.
- Preserve the relevant versions and conflict decision in the action history.

**Scope:** This is the working conflict-handling rule for Context Note Update v0.1. It does not finalize the general conflict policy for every future integration or action type.

**Open details:** Exact UI presentation; how the user chooses to resolve the conflict; whether a separate merge/rebase option may be introduced later; version identifier and concurrency mechanism; exact audit fields.

## Working Decision 6 — Context Note Update v0.1 pre-write version check

**Selected direction:** A — Atomic version check. **WORKING DRAFT — NOT FINAL-LOCKED.**

Immediately before saving a prepared context-note update, the system must ensure the target note is still at the exact version reviewed and approved for that update. The version comparison and write must be performed as one atomic conditional operation.

- The write succeeds only if the current note version matches the expected approved version.
- If the version no longer matches, reject the write, preserve the newer content, pause the action, and follow Working Decision 5: show the current and proposed versions and ask the user how to proceed.
- Do not fall back silently to a non-atomic check-then-write sequence.
- If the storage layer cannot provide atomic conditional writes, do not claim this guarantee; hold implementation of this path until a safe supported mechanism is selected.
- After a conflict is resolved, prepare/review the updated request and revalidate current authorization, policy, approval binding, and required evidence before attempting a new conditional write.
- Record the expected version, conflict result, subsequent user decision, and verified outcome in the action history, subject to the concrete data design still being undecided.

**Scope:** This is the working pre-write concurrency rule for Context Note Update v0.1. It does not select a database, versioning scheme, API endpoint, or general concurrency policy for every future action.

**Open details:** Storage mechanism and version identifier; transaction/conditional-write API; how a new version is prepared and approved after conflict resolution; idempotency and lost-ack handling; audit fields and retention.

## Working Decision 7 — Context Note Update v0.1 save-timeout handling

**Selected direction:** A — Reconcile before recovery. **WORKING DRAFT — NOT FINAL-LOCKED.**

If a save request times out and the system cannot determine whether the write completed:
- Mark the action outcome `UNKNOWN / UNVERIFIED`; a timeout alone is neither success nor failure.
- Perform an authorized, bounded read/reconciliation of the actual note state before any recovery action.
- If authoritative evidence establishes that the intended update was saved, verify the saved content/version and record the verified result.
- If evidence establishes that the intended update was not saved, do not automatically retry; pause and follow the applicable recovery/approval flow.
- If the state remains ambiguous or cannot be inspected reliably, keep the outcome `UNKNOWN / UNVERIFIED`, pause affected work, explain what is known and unknown, and ask the user how to proceed.
- Never automatically retry, roll back, or repair.
- Preserve the last verified state, the timeout, reconciliation evidence, and subsequent verified findings in the action history. Do not silently overwrite the original uncertainty.
- Revalidate current authorization, policy, expected version, approval binding, and required evidence before any newly authorized write.

**Scope:** This is the working timeout-handling rule for Context Note Update v0.1. It does not define timeout durations, a specific storage API, retry policy for unrelated actions, or the physical audit schema.

**Open details:** Reconciliation bounds and read source; how to establish authoritative state; handling a lost write acknowledgement; timeout thresholds; action/request identifiers and idempotency strategy; escalation/ownership when state remains unknown.

## Working Decision 8 — Context Note Update v0.1 duplicate-write prevention

**Selected direction:** A — Unique Operation ID plus reconciliation. **WORKING DRAFT — NOT FINAL-LOCKED.**

Every logical note-update operation receives a unique Operation ID that remains associated with the original request and its audit history.

If a save request times out or its acknowledgement is lost:
- Mark the outcome `UNKNOWN / UNVERIFIED` unless authoritative evidence establishes the result.
- Inspect/reconcile the status of the original operation using an authorized, bounded method.
- Do not automatically create a new operation or automatically retry the write.
- If the original operation's status or outcome cannot be established, pause affected work and explain what is known and unknown; ask the user how to proceed.
- If the result is authoritatively verified, record the actual result and preserve the original timeout/reconciliation history.
- Before any newly authorized write, revalidate current authorization, policy, expected note version, approval binding, and required evidence.
- The Operation ID identifies the logical attempt but, by itself, does not guarantee exactly-once execution. The implementation must not claim duplicate prevention beyond guarantees actually provided by the storage/action mechanism.

**Scope:** This is the working duplicate-write rule for Context Note Update v0.1. It does not select an ID format, persistence technology, idempotency API, or general retry policy for other actions.

**Open details:** Operation ID format and lifecycle; how the ID is durably associated with the write before dispatch; authoritative operation-status source; retention and lookup window; storage support for idempotency/conditional writes; handling of lost acknowledgements and process restarts.

## Working Decision 9 — Context Note Update v0.1 unknown-outcome escalation

**Selected direction:** A — Bounded authorized reconciliation, then inform the user and remain paused if status is unknown. **WORKING DRAFT — NOT FINAL-LOCKED.**

When the original operation's outcome remains unknown:
- Perform only bounded reconciliation through authorized sources/methods.
- If authoritative evidence establishes the result, record the verified outcome while preserving the timeout and reconciliation history.
- If status remains unknown after the permitted reconciliation, keep the outcome `UNKNOWN / UNVERIFIED`, pause the affected action/work, and inform the user what is known and unknown.
- Ask the user how to proceed. Do not automatically retry, create a new operation, roll back, or repair.
- Before any later authorized write or consequential recovery, revalidate current authorization, policy, expected note version, approval binding, and required evidence.

**Scope:** This is the escalation rule for Context Note Update v0.1, alongside Decisions 7 and 8. It does not define a universal escalation policy for all future actions.

**Open details:** Reconciliation count/time bounds, authoritative status source, who receives escalation if the user is unavailable, notification channel/acknowledgement, and ownership/expiry for unresolved cases.

## Working Decision 10 — User unavailable while outcome remains unknown

**Selected direction:** Pause and wait for the user. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, if the operation outcome remains `UNKNOWN / UNVERIFIED` and the user is unavailable:
- Keep the affected action paused until the user returns and provides direction.
- Do not perform automatic further work, retry, rollback, repair, or recovery.
- Preserve the last verified state, original operation identity, timeout, reconciliation evidence, and uncertainty/history.
- When the user returns, explain what is known and unknown and ask how to proceed.
- Any later action must go through the applicable authorization, policy, version, approval, and evidence checks.

**Scope:** This rule applies to the unresolved Context Note Update v0.1 operation; it does not define a universal escalation or notification policy for all future actions.

**Open details:** How user availability is detected; whether and how to notify the user; retention and ownership of paused work; and any time-based escalation for cases other than this selected rule.

## Working Decision 11 — Resume revalidation after user returns

**Selected direction:** A — Revalidate all relevant current state. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when the user returns and asks to continue a paused operation:
- Revalidate all relevant current state, including the original operation's status, current note state/version, current authorization, applicable policy, approval validity/binding, and required evidence.
- The user's request to continue is direction to review/resume, not proof that the previous operation did or did not complete, nor automatic authorization to repeat a write.
- If the original operation outcome, current state, authority, approval, or required evidence remains uncertain, keep the action paused and explain what is known and unknown.
- Do not automatically create a new operation, retry, rollback, repair, or recovery.
- Any later write or recovery must follow the applicable authorization, policy, version, approval, and evidence requirements.

**Scope:** This rule applies to resuming the paused Context Note Update v0.1 operation. It does not finalize a universal resume policy for all future actions.

**Open details:** Exact authoritative status and note-version sources; which state checks can be performed automatically; approval expiry/revocation rules; and how to present revalidation results to the user.

## Working Decision 12 — Intended note update already applied

**Selected direction:** A — Verify, record, and stop. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, if revalidation finds that the intended update may already have been saved:
- Verify the current note content and version against the intended update using authoritative evidence.
- Only when content and version are sufficiently verified as the intended result, record the original operation's verified outcome and preserve the timeout/reconciliation history.
- Do not issue another write or create a replacement operation for the same intended update.
- If the current content/version does not match, or the evidence is incomplete or ambiguous, do not assume success; keep the action paused and explain what is known and unknown.
- Any separate follow-up change requires its own applicable authorization, policy, version, approval, and evidence checks.

**Scope:** This rule applies to Context Note Update v0.1 and the original intended update. It does not authorize unrelated follow-up changes.

**Open details:** The authoritative source for current note content/version, the exact comparison semantics for the intended content, and the evidence threshold for marking the original operation verified.

## Working Decision 13 — Content matches but version/operation metadata differs

**Selected direction:** Require both content and version verification; do not treat content match alone as sufficient. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, if the current note content appears to match the intended update but the expected version or operation metadata differs:
- Do not mark the original operation verified based on content match alone.
- Keep the action paused while the discrepancy is investigated through authorized, bounded reconciliation.
- Verify current note content and version against authoritative state, and inspect available original-operation metadata/evidence.
- Preserve the discrepancy, original timeout, reconciliation attempts, and subsequent findings in history; do not silently rewrite or erase the mismatch.
- If the discrepancy cannot be authoritatively explained, retain `UNKNOWN / UNVERIFIED`, explain what is known and unknown, and ask the user how to proceed.
- Do not automatically write again, create a replacement operation, retry, roll back, or repair.
- Any separately authorized follow-up action must pass applicable authorization, policy, version, approval, and evidence checks.

**Scope:** This rule applies to Context Note Update v0.1 when content appears to match but version or operation metadata differs. It does not define general reconciliation semantics for all actions.

**Open details:** Which metadata is authoritative, how version changes are interpreted, which bounded reconciliation sources are available, and the evidence threshold for explaining a discrepancy.

## Working Decision 14 — User-facing uncertainty when evidence is unavailable

**Selected direction:** A — Provide a transparent uncertainty report. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when authoritative evidence needed to verify the original operation is unavailable:
- Clearly report the intended action.
- Distinguish the observed current state from what could not be verified.
- Identify the missing/unavailable evidence or verification source, without overstating what it proves.
- State which actions were not taken (including no automatic new write, retry, rollback, repair, or recovery).
- Keep the outcome `UNKNOWN / UNVERIFIED` when the required evidence is insufficient.
- Keep the affected action paused and ask the user for direction; do not automatically resume.
- Preserve the original operation, timeout, observations, reconciliation attempts, and uncertainty in history.

**Scope:** This user-facing uncertainty report rule applies to Context Note Update v0.1. It does not define a universal reporting format for every future action.

**Open details:** Exact report format and fields, evidence-source naming, sensitivity/redaction rules, and whether/how the user can authorize a bounded further investigation.

## Working Decision 15 — User-authorized investigation boundaries

**Selected direction:** A — Bounded read-only investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when the user authorizes further investigation after an operation remains `UNKNOWN / UNVERIFIED`:
- Perform only bounded, read-only investigation using permitted evidence sources to reconcile the original operation and gather relevant observations.
- Preserve the original operation ID, timeout, observations, reconciliation attempts, evidence, and uncertainty history.
- Report findings and clearly distinguish verified facts from incomplete or ambiguous evidence.
- Do not write or modify the note, create a replacement operation, retry the write, rollback, repair, or perform recovery as part of this investigation.
- If the available evidence cannot resolve the outcome within the allowed investigation boundary, remain `UNKNOWN / UNVERIFIED`, keep the affected action paused, and report the unresolved state to the user.
- Any subsequent write, retry, rollback, repair, recovery, or other consequential action requires separate authorization and the applicable policy, risk, authority, approval, and verification checks.

**Scope:** This is specific to Context Note Update v0.1 and does not establish a universal investigation policy for all future actions.

**Open details:** Which evidence sources are permitted, exact bounds on investigation steps/time/resources, how source authority is established, and how findings are presented.

## Working Decision 16 — Evidence source authority and trust

**Selected direction:** A — Pre-approved authoritative sources only. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, investigation to verify the original operation may rely only on pre-approved authoritative evidence sources relevant to that operation.
- Do not automatically treat an alternative source as authoritative merely because an approved source is unavailable.
- If an approved source is unavailable, stale, incomplete, or conflicting, preserve the uncertainty and keep the operation `UNKNOWN / UNVERIFIED` unless the required verification can be established from an approved source.
- Continue to follow Decision 15: investigation remains bounded and read-only; no note write, replacement operation, retry, rollback, repair, or recovery occurs as part of investigation.
- Preserve source observations and the evidence/reconciliation history so the basis for any conclusion remains traceable.

**Scope:** This rule applies to evidence-source authority for Context Note Update v0.1. It does not define source lists for other actions or integrations.

**Open details:** The actual pre-approved source list, how sources are approved and versioned, freshness requirements, conflict-handling rules, and source-specific evidence thresholds.

## Working Decision 17 — Conflicting authoritative evidence

**Selected direction:** A — Preserve conflict and pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, if two pre-approved authoritative sources provide conflicting evidence about the original operation:
- Preserve both source observations, including available timestamps and relevant provenance.
- Do not silently prefer one source or resolve the conflict by assumption.
- Keep the operation outcome `UNKNOWN / UNVERIFIED` while the conflict prevents required verification.
- Keep the affected action paused and explain the conflict to the user, distinguishing each source's observation from any unresolved conclusion.
- Continue to follow Decisions 15 and 16: any investigation remains bounded and read-only and uses only pre-approved authoritative sources. Do not write, retry, create a replacement operation, rollback, repair, or perform recovery as part of reconciliation.

**Scope:** This conflict-handling rule applies to Context Note Update v0.1. It does not establish general source precedence for other actions.

**Open details:** Exact evidence fields to preserve, how timestamps/provenance are represented, and whether future source-specific precedence rules may be approved for defined cases.

## Working Decision 18 — User notification on evidence conflict

**Selected direction:** A — Notify immediately; suppress duplicate alerts; update on meaningful status changes. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when conflicting authoritative evidence blocks required verification and causes the affected action to be paused:
- Notify the user as soon as the conflict is identified as blocking required verification and the action is paused.
- Suppress repeated duplicate alerts for the same unresolved conflict when there is no meaningful change.
- Send an update when the status or evidence changes meaningfully.
- Keep the action paused and the outcome `UNKNOWN / UNVERIFIED` while required verification remains unresolved.
- Explain the conflict, what is known versus unresolved, and that the action is paused. Minimize sensitive information in the notification.
- Preserve notification and status-change history for traceability.

**Scope:** This notification rule applies to evidence conflicts in Context Note Update v0.1. It does not establish a universal notification policy for all future actions.

**Open details:** Notification channel, delivery/acknowledgement semantics, what qualifies as a meaningful change, retry/escalation behavior for failed notifications, and sensitive-data redaction rules.

## Working Decision 19 — Notification delivery failure

**Selected direction:** A — Record failure, use only explicitly permitted bounded retries, then preserve failure/unknown delivery status and keep the action paused. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when a notification about unresolved evidence conflict cannot be confirmed as delivered:
- Record the notification attempt and the observed delivery result accurately.
- Retry only when explicitly permitted by a defined bounded retry rule; do not invent retry behavior or continue indefinitely.
- If delivery remains unconfirmed, preserve the appropriate failure or `UNKNOWN / UNVERIFIED` delivery status; do not claim the user was notified or received the message without evidence.
- Keep notification delivery status separate from the underlying operation/action status. A notification failure does not prove that the original operation failed, and a successful send request does not by itself prove receipt.
- Keep the affected action paused while the underlying outcome remains unresolved.
- Preserve notification attempts, delivery evidence, status changes, and retry history for traceability.

**Scope:** This rule applies to conflict notifications in Context Note Update v0.1. It does not define a universal notification transport or retry policy.

**Open details:** Retry count/backoff/time limits, what delivery evidence is authoritative, how to classify failed versus unknown delivery, permitted fallback channels, and escalation if the user remains unreachable.


## Working Decision 20 — Notification delivery evidence

**Selected direction:** C — Keep request/provider acceptance, delivery confirmation, and user read/acknowledgement as distinct states. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1 and its conflict notifications:
- Distinguish at minimum: notification request accepted by the sending system, notification accepted by the provider, delivery explicitly confirmed, and user read/acknowledgement when supported.
- Do not infer provider acceptance from a local send request, delivery from provider acceptance, or reading/acknowledgement from delivery.
- Mark each status only when supported by its corresponding evidence. If delivery or acknowledgement cannot be established, preserve `UNKNOWN / UNVERIFIED` for that stage rather than overstating success.
- Keep notification lifecycle status separate from the original operation/action outcome.
- Preserve status transitions and the evidence supporting them.

**Scope:** This is a status/evidence distinction, not a decision about a specific provider, channel, or delivery-receipt implementation.

**Open details:** Which providers/channels expose authoritative delivery receipts, how read/acknowledgement is defined per channel, evidence freshness and retention, duplicate/out-of-order events, and user-facing status wording.


## Working Decision 21 — Undeliverable notification fallback

**Selected direction:** A — Pause and wait as the baseline. **WORKING DRAFT — NOT FINAL-LOCKED.**

For Context Note Update v0.1, when bounded notification attempts have been exhausted or delivery remains unconfirmed:
- Preserve the supported delivery failure or `UNKNOWN / UNVERIFIED` status and the notification-attempt history.
- Do not invent or automatically select a fallback channel, contact, or escalation route.
- Keep the affected action paused while its underlying outcome remains unresolved; notification failure does not change the operation's outcome.
- When the user returns, present the pending issue clearly, distinguishing known facts from unknown delivery and operation states.
- Any future fallback channel or escalation must be explicitly authorized and governed by applicable policy, permissions, privacy, and bounded-attempt rules.

**Scope:** This is the baseline for undeliverable conflict notifications in Context Note Update v0.1. It does not prohibit adding explicitly pre-authorized fallback channels or risk-based escalation through a later reviewed decision.

**Open details:** How user return/availability is detected, how the pending issue is surfaced, retention and ownership while waiting, and whether approved fallback channels or risk-based escalation should be added later.


## Working Decision 22 — Surfacing unresolved issues when the user returns

**Selected direction:** C — Persistent pending-issues panel plus return alert. **WORKING DRAFT — NOT FINAL-LOCKED.**

When the user returns while an issue remains unresolved:
- Show a prominent alert on return/session start.
- Keep the issue visible in a persistent pending-issues panel until it is resolved or explicitly dismissed under the applicable rules.
- Dismissing the alert does not resolve, verify, or otherwise clear the underlying issue.
- Continue to distinguish known facts from unknown/unverified states; do not imply that the original operation or notification succeeded merely because the user returned.

**Scope:** Applies to unresolved notification-delivery issues and their related paused operations in Context Note Update v0.1. Other pending-issue categories may reuse the pattern only after appropriate review.

**Open details:** What counts as a user returning/session start, alert frequency and deduplication, panel ordering, who may dismiss an item, what explicit dismissal means, and retention/ownership rules.


## Working Decision 23 — Meaning of pending issue acknowledgement/dismissal

**Selected direction:** B — Acknowledge, but keep unresolved. **WORKING DRAFT — NOT FINAL-LOCKED.**

- The user can mark a pending issue as seen/acknowledged.
- Record the acknowledgement as its own event.
- Acknowledgement does not resolve, verify, or clear the issue; its unresolved status remains visible in the persistent pending-issues panel.
- Acknowledgement does not authorize resuming a paused action, retrying, or performing recovery.
- Resolution and any consequential follow-up continue to use their own evidence, authorization, and verification requirements.

**Scope:** Applies to pending notification-delivery issues and related paused operations in Context Note Update v0.1.

**Open details:** Whether acknowledgement can be reversed, how it is displayed, who can acknowledge on a shared project, and the exact event/audit fields.


## Working Decision 24 — Shared-project acknowledgement permissions

**Selected direction:** C — Any project member may acknowledge; resolution remains separately restricted. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Authorized project members may mark a pending issue as seen/acknowledged.
- Acknowledgement records awareness only; it does not resolve or verify the issue, change delivery/operation status, or authorize resumption, retry, or recovery.
- Resolution and consequential follow-up require separate applicable authorization, evidence, and verification.
- Acknowledgement and resolution permissions must remain distinct.

**Open details:** Exact definition of project membership and authorized membership, treatment of guests/external collaborators, audit fields for the acknowledging actor, and role/permission model for resolution.


## Working Decision 25 — Conflicting instructions in shared projects

**Selected direction:** C — Role-based authority with pause on ambiguity. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Defined roles and permissions determine which members may authorize which actions.
- One member's instruction does not automatically override another member's instruction.
- If authority is unclear, overlapping, or instructions conflict, pause the affected action and request clarification through the applicable authorized process.
- Mandatory safety restrictions, evidence requirements, and verification requirements remain in force; role authority does not bypass them.
- Acknowledgement permission remains distinct from issue-resolution and action-execution authority.

**Open details:** Role hierarchy, conflict resolution/escalation path, what counts as a conflicting instruction, handling of queued/in-flight actions, and how authorization changes affect pending approvals.


## Working Decision 26 — Conflicting instructions when the project owner is unavailable

**Selected direction:** C — Use a delegate if authorized; otherwise pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

- If a pre-designated delegate has explicit authority for the specific conflict/action, the delegate may act only within that defined scope.
- The project owner's unavailability does not automatically transfer authority to another member.
- If no suitable authorized delegate exists, or authority/scope is unclear, keep the affected action paused and wait for the owner or another properly authorized clarification.
- Existing safety, evidence, approval, and verification requirements remain in force.
- Preserve the conflict, delegation/authority basis, pause, and resolution history.

**Open details:** How delegates are designated and revoked, their exact scope, delegation expiry, owner-unavailability criteria, escalation route, and effects on queued/in-flight actions.


## Working Decision 27 — Delegate authority expiry or revocation

**Selected direction:** C — State-aware authority revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

- When delegation expires or is revoked, block new actions relying on that delegation.
- Pause pending affected actions and revalidate current authority before any resumption.
- For in-flight actions, assess the actual execution state safely; do not blindly cancel, retry, or roll back.
- If an in-flight outcome is unknown, use the approved bounded reconciliation process and apply fresh-authorization requirements before any consequential follow-up.
- Preserve delegation change, action state, reconciliation evidence, and authorization history.

**Open details:** Exact revocation propagation timing, authoritative source of delegation state, in-flight action classes and safe-stop behavior, reauthorization requirements, and how queued work is handled.


## Working Decision 28 — Previously approved action after delegate revocation

**Selected direction:** C — Revalidate approval binding and current authority. **WORKING DRAFT — NOT FINAL-LOCKED.**

For an action approved before a delegate's authority expires or is revoked but not yet started:
- Revalidate who approved it, the authority under which approval was granted, its scope, and whether it remains valid under current policy.
- Do not automatically assume the approval remains valid or is automatically invalidated solely by the revocation.
- If approval validity or authority is ambiguous, pause and obtain fresh authorization through the applicable process.
- Recheck action state, target, scope, and relevant policy before dispatch; preserve the original approval and revocation history.

**Open details:** Whether revocation affects approvals granted under delegated authority by default, how approval validity is represented/versioned, and the rules for already-queued approvals.


## Working Decision 29 — Scope or parameter changes after approval

**Selected direction:** C — Strict binding with explicit materiality rules. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Bind each approval to the exact action, target, version, scope, and relevant parameters reviewed.
- Changes explicitly classified as non-material may be handled under approved policy rules.
- Material changes require revalidation and fresh approval where applicable.
- If materiality or approval applicability is ambiguous, pause and clarify rather than silently reusing the prior approval.
- Preserve the originally approved request, any changed request, the classification/revalidation result, and approval history.

**Open details:** Exact materiality criteria by action type, who approves those criteria, which changes are always material, and how changes are detected/versioned.


## Working Decision 30 — Who classifies material changes after approval?

**Selected direction:** C — Policy rules plus AI assistance; ambiguity means pause. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Approved, explicit policy rules are authoritative for materiality classification.
- AI may identify, compare, and explain differences between the approved and proposed action.
- AI must not invent exceptions or independently override policy boundaries.
- If the rules do not cover the change, or classification/approval applicability is ambiguous, pause and seek clarification.
- Preserve the compared versions, identified differences, applied rule, AI explanation where used, classification result, and approval/revalidation history.

**Open details:** Who owns and approves materiality rules, how rules are versioned, how uncovered cases are escalated, and how AI classification suggestions are recorded.


## Working Decision 31 — Approval validity and expiry

**Selected direction:** C — Risk-based expiry plus pre-execution revalidation. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Approval validity/expiry is governed by risk and approved policy, rather than assuming every approval lasts indefinitely or uses one universal fixed expiry.
- Before execution, revalidate current authority, approved action/scope, relevant policy, and required evidence.
- If approval has expired or its validity is ambiguous, pause and obtain fresh approval through the applicable process.
- Preserve approval timestamp, applicable validity/expiry rule, revalidation evidence, and any renewal or fresh approval history.

**Open details:** Risk-to-expiry mapping, which evidence/policy changes invalidate approval, expiry calculation and clock source, notification timing, and renewal workflow.


## Working Decision 32 — Policy changes after approval

**Selected direction:** C — Revalidate current policy before execution. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Before execution, check the current applicable policy rather than relying solely on the policy that existed when approval was granted.
- If the updated policy prohibits the action or materially changes the approval conditions, pause and follow the required reapproval path.
- If the policy update is not relevant to the approved action, proceed only as permitted by documented rules.
- Preserve the policy version used at approval, current policy version, revalidation result, and any pause/reapproval history.

**Open details:** How relevance/materiality is determined, authoritative policy version source, emergency policy changes, and handling of policy changes during an in-flight action.


## Working Decision 33 — Policy update during an in-flight action

**Selected direction:** C — Risk-aware pause/containment plus state verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

- When policy changes during execution, assess the updated policy's applicability and the action's actual execution state.
- If continuing may be prohibited or unsafe, pause or contain the action at a safe boundary where feasible.
- Verify actual state before choosing further action. Do not blindly cancel, retry, roll back, or assume the action can safely stop.
- If state or policy impact remains uncertain, keep the action safely held where possible and request authorized review.
- Preserve policy version/change, observed execution state, containment/verification steps, and review decisions.

**Open details:** Defining safe boundaries by action type, urgent policy-change handling, who can authorize continuation/recovery, and verification sources for partially completed actions.


## Working Decision 34 — Action cannot be safely paused

**Selected direction:** C — Action-specific safe-stop policy plus containment. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Follow pre-defined, action-specific safe-stop rules rather than assuming every action can safely pause or must always be interrupted.
- If safe interruption is not possible, do not add unapproved new steps; use only permitted containment, monitor and verify actual state, and request authorized review.
- Action completion is not automatically evidence that the result was safe or policy-compliant.
- Preserve the applicable safe-stop rule, policy change, containment steps, observed state, verification evidence, and review history.

**Open details:** Action-specific safe-stop catalogue, permitted containment options, monitoring/verification sources, escalation timing, and authority for post-event recovery.


## Working Decision 35 — Who authorizes recovery after a safe-stop?

**Selected direction:** C — Risk- and role-based fresh authorization. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Treat recovery as a separate action; the original action's approval does not automatically authorize recovery.
- Before recovery, validate current authority, risk, applicable policy, and actual state.
- Consequential recovery requires fresh approval from an appropriately authorized person.
- Emergency permissions may be used only where explicitly defined by policy and within their scope.
- Preserve recovery rationale, state/evidence, authority and approval details, and execution/verification history.

**Open details:** Recovery risk tiers, eligible roles, emergency authority boundaries, approval binding/expiry, and verification requirements for each recovery type.


## Working Decision 36 — Recovery approver unavailable

**Selected direction:** C — Pre-authorized escalation path. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Use a pre-designated, appropriately authorized alternate or emergency role only within explicitly defined policy and scope.
- Approver unavailability does not automatically transfer authority to the AI or another person.
- If no applicable pre-authorized path exists, keep the action safely held where possible, monitor state, and escalate; do not execute consequential recovery without authorization.
- Preserve the unavailable approver status, escalation path used or unavailable, authority checks, state/evidence, and recovery decision history.

**Open details:** Eligible alternate/emergency roles, scope boundaries, escalation timing, availability detection, and what monitoring/containment is permitted while waiting.


## Working Decision 37 — Scope of emergency recovery authority

**Selected direction:** C — Narrow predefined authority plus strict limits. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Emergency recovery authority is limited to explicitly authorized recovery actions and defined scope, risk limits, and expiry.
- Each action still requires applicable state/evidence checks and an auditable record.
- Actions outside the defined limits require fresh authorization or a safe hold.
- Emergency authority is not a general permission to do whatever seems necessary.

**Open details:** Emergency-role eligibility, action catalogue, risk thresholds, expiry/revocation behavior, required evidence, and post-action review.


## Working Decision 38 — Review after emergency recovery

**Selected direction:** C — Audit every recovery plus risk-based independent review. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Every emergency recovery receives an audit record and outcome verification.
- Higher-risk or exceptional cases require independent human review.
- Keep the issue unresolved until all required checks are complete; apparent recovery success alone does not close it.
- Preserve recovery rationale, authorization, execution evidence, verification result, reviewer/decision, and closure status.

**Open details:** Risk thresholds for independent review, reviewer independence/eligibility, required verification evidence, review deadlines, and criteria for closure or residual-risk acceptance.


## Working Decision 39 — Who accepts residual risk after recovery?

**Selected direction:** C — Risk-based acceptance authority plus explicit record. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Policy defines who may accept each level of residual risk.
- AI explains the remaining risk, supporting evidence, and uncertainty; it does not itself accept the risk.
- The authorized person explicitly accepts or rejects the residual risk, with the decision recorded.
- Prohibited or unbounded risks cannot be bypassed through residual-risk acceptance.

**Open details:** Risk levels and thresholds, authorized roles, evidence requirements, acceptance expiry/review, and whether/how accepted residual risk affects closure.


## Working Decision 40 — Review of accepted residual risk

**Selected direction:** C — Risk-based review plus change-triggered reassessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Set review timing according to residual-risk level.
- Reassess earlier when relevant policy, evidence, environment, or assumptions change.
- Expired or invalid acceptance must not be treated as current approval.

**Open details:** Risk-to-review intervals, material-change triggers, expiry semantics, responsible reviewer, notification/escalation, and how reassessment affects issue closure.


## Working Decision 41 — Risk increases during reassessment

**Selected direction:** C — Risk-based revalidation plus temporary safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

- A material risk increase means the existing acceptance may no longer be sufficient.
- Apply proportionate temporary safeguards; pause or contain affected consequential actions when needed.
- Verify the evidence and route the changed risk for authorized reassessment.
- A risk-increase signal is not, by itself, conclusive proof; preserve uncertainty and assess the evidence.

**Open details:** Material-increase thresholds, temporary safeguard catalogue, who can authorize continued operation, evidence standards, and handling of active/in-flight actions.


## Working Decision 42 — System behavior while risk reassessment is pending

**Selected direction:** C — Targeted hold on affected actions. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Proportionately pause or restrict consequential actions potentially affected by the risk change.
- Unaffected work may continue only when it is demonstrably safe and not dependent on the unresolved risk.
- Make uncertainty, affected scope, and active restrictions visible.
- Avoid freezing the entire project without need; do not let unaffected-work continuity bypass required safeguards.

**Open details:** How affected scope is determined, safe-continuation criteria, hold owner, status labels, escalation timing, and how to handle dependencies on held work.


## Working Decision 43 — Authority to lift a risk hold

**Selected direction:** C — Risk-based authorized release plus verified prerequisites. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Policy defines who may release a hold at each risk level.
- Required evidence and safety prerequisites must be verified before release.
- AI alone cannot lift a hold on consequential actions.
- Approval cannot bypass prohibited risks or mandatory safety constraints.

**Open details:** Risk-to-role mapping, release evidence, independence requirements for high-risk cases, emergency release rules, and how the release is recorded.


## Working Decision 44 — Verification after risk-hold release

**Selected direction:** C — Scope-specific pre-resume verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Before resuming, recheck the current state, authority, policy, evidence, and prerequisites for affected actions and their dependencies.
- Resume only the scope that has passed the required checks.
- If state or required evidence remains unknown, keep the affected scope on hold.
- Hold-release approval is not itself proof that resuming execution is safe.

**Open details:** Required checks by action/risk tier, authoritative evidence sources, dependency boundaries, recheck freshness, and handling of partial/in-flight work.


## Working Decision 45 — Verification fails after risk-hold release

**Selected direction:** C — Restore hold on affected scope plus bounded diagnosis. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Keep actions with failed or unknown checks on hold.
- Allow only policy-authorized, bounded diagnosis; no blind retries or unsafe rollback.
- Verified unaffected work may continue if independent of the held scope and demonstrably safe.
- Record failed checks, evidence, diagnostic steps, and resulting status.

**Open details:** Diagnostic bounds and permissions, retry criteria, containment rules, dependency scope, escalation, and how a new release is authorized after remediation.


## Working Decision 46 — Escalation when diagnosis cannot resolve the issue

**Selected direction:** C — Risk-based escalation plus bounded investigation. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Investigation stays within policy-defined time, budget, and evidence limits.
- If unresolved at those limits, escalate according to risk level and the authority required.
- Keep the affected scope held while the issue remains unresolved and safeguards remain necessary.
- Escalation failure or handoff is not resolution.

**Open details:** Risk-to-escalation mapping, time/budget/evidence limits, escalation recipients, response deadlines, unavailable-authority handling, and status/notification rules.


## Working Decision 47 — Escalation recipient unavailable

**Selected direction:** C — Pre-authorized escalation chain plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Use alternate authorities and response deadlines only as defined by policy.
- If no valid alternate is available, keep the affected scope held and continue only permitted monitoring.
- Make escalation status and lack of response visible.
- Unavailability does not automatically grant AI additional decision-making authority.

**Open details:** Alternate-role mapping, response deadlines, availability detection, permitted monitoring, escalation channels, and handling of prolonged unavailability.


## Working Decision 48 — Escalation deadline missed

**Selected direction:** C — Deadline-based escalation ladder plus safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

- When a policy-defined response deadline expires without a response, advance escalation only along the policy-authorized ladder.
- Record escalation attempts and delivery/acknowledgement status.
- If no valid authority responds or is available, keep the affected scope held and show escalation as overdue.
- A missed deadline does not imply approval, resolution, or expanded AI authority.

**Open details:** Deadline rules by risk, ladder ownership, repeat/escalation cadence, delivery-failure handling, overdue notifications, and prolonged non-response handling.


## Working Decision 49 — All escalation levels exhausted

**Selected direction:** C — Terminal escalation state plus controlled safe hold. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Mark the escalation chain as exhausted; preserve attempts, recipients, timestamps, delivery/acknowledgement state, and unresolved status.
- Record the responsible owner and next review condition where policy defines them.
- Continue only explicitly permitted monitoring while the affected scope remains held.
- The hold may be lifted only after a valid authorized decision and required verification; exhausted escalation does not grant AI new authority.

**Open details:** Owner assignment rules, review triggers/cadence, terminal-state labels, visibility/notification requirements, and permitted monitoring scope.


## Working Decision 50 — Review after all escalation levels are exhausted

**Selected direction:** C — Risk-based review plus meaningful-change triggers. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Set review cadence according to risk.
- Trigger earlier review when relevant evidence, policy, authority availability, or issue state materially changes.
- A review trigger initiates reassessment only; it does not resolve the issue or release the hold.
- Keep the affected scope held until the applicable authorized decision and verification requirements are satisfied.

**Open details:** Risk-to-review intervals, material-change definitions, trigger sources, ownership, missed-review handling, notifications, and resource limits.


## Working Decision 51 — Conflicting evidence during review

**Selected direction:** C — Preserve conflict plus bounded verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Preserve both conflicting evidence items with timestamps and provenance; do not silently prefer the newer or older item.
- Perform bounded verification using approved authoritative sources only.
- If the conflict remains unresolved, keep the status uncertain and the affected scope held.
- Resolution requires applicable authorized reassessment and verification; a new review alone does not release the hold.

**Open details:** Authoritative-source precedence, verification limits, evidence freshness, conflict-resolution ownership, and user-facing status/notification rules.


## Working Decision 52 — Who decides status when conflicting evidence remains unresolved?

**Selected direction:** C — Risk-based authorized review plus explicit uncertainty. **WORKING DRAFT — NOT FINAL-LOCKED.**

- AI explains the conflict, evidence, and potential impact.
- An authorized reviewer, as defined by policy, determines whether evidence is sufficient, more verification is needed, or uncertainty may be accepted.
- AI alone does not resolve consequential status when evidence remains conflicting.
- Keep affected scope held unless the applicable authorization and verification requirements permit release.

**Open details:** Reviewer role mapping by risk, evidence sufficiency criteria, permissible uncertainty acceptance, independence requirements, decision expiry/review, and hold-release implications.


## Working Decision 53 — Authorized reviewer cannot resolve uncertainty

**Selected direction:** C — Explicit unresolved disposition plus future review. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Record uncertainty and missing/insufficient evidence explicitly.
- The authorized reviewer may leave the issue unresolved rather than being forced to select an unsupported conclusion.
- Maintain applicable holds and safeguards on the affected scope.
- Reassess on risk-based review cadence or when relevant new evidence arrives; reassessment does not itself release a hold.

**Open details:** Unresolved-state labels, review cadence, evidence-change triggers, ownership, notification rules, and criteria for accepting residual uncertainty.


## Working Decision 54 — Accepting unresolved uncertainty to proceed

**Selected direction:** C — Explicit risk acceptance plus bounded safe continuation. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Unresolved uncertainty may be accepted only where applicable policy permits it and an authorized person explicitly accepts the documented residual risk.
- Record the uncertainty, evidence gaps, accepted scope, rationale, and validity conditions.
- Continue only work demonstrably safe and independent of the unresolved issue.
- Prohibited or otherwise disallowed high-risk actions remain blocked; AI cannot accept risk on its own.
- Risk acceptance does not itself satisfy other required authorization or verification conditions.

**Open details:** Policy eligibility, risk-to-authority mapping, acceptance expiry/review triggers, safe-independence criteria, and required evidence.


## Working Decision 55 — Accepted residual risk changes later

**Selected direction:** C — Change-triggered reassessment plus targeted safeguards. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Material changes in evidence, policy, environment, or relevant assumptions trigger revalidation of the accepted residual risk.
- Apply proportionate safeguards and restrict or hold affected consequential actions when needed.
- Work may continue only where it is demonstrably safe and independent of the changed risk.
- Existing risk acceptance is not automatically sufficient after a material change.
- Preserve the change, reassessment, safeguards, authority, and decision history.

**Open details:** Material-change criteria, risk thresholds, safeguard catalogue, reassessment authority, evidence freshness, and handling of in-flight actions.


## Working Decision 56 — Who chooses temporary safeguards during risk reassessment?

**Selected direction:** C — Pre-approved safeguard policy plus escalation. **WORKING DRAFT — NOT FINAL-LOCKED.**

- The AI may identify or apply only predefined safeguards explicitly authorized by policy.
- If policy does not cover the situation or the case is ambiguous, hold affected consequential work and request authorized review.
- Temporary safeguards do not resolve the underlying risk or independently release a hold.
- Preserve the triggering change, policy rule, selected safeguard, scope, and review/escalation history.

**Open details:** Safeguard catalogue, applicability criteria, authority to activate each safeguard, ambiguity thresholds, escalation timing, and handling of in-flight actions.


## Working Decision 57 — Verifying temporary safeguard effectiveness

**Selected direction:** C — Independent, risk-based verification. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Verify safeguard effectiveness using policy-defined authoritative evidence, proportionate to risk.
- A successful apply/command response is not, by itself, proof that the safeguard is effective.
- If verification is unavailable, conflicting, or uncertain, do not mark the safeguard verified.
- Keep affected consequential work on hold and route the case for authorized review/escalation.
- Preserve the safeguard action, evidence, verification result, uncertainty, and follow-up history.

**Open details:** Authoritative verification sources, risk-based verification depth, evidence freshness, independent-check requirements, and escalation timing.


## Working Decision 58 — Previously verified safeguard later becomes ineffective

**Selected direction:** C — Revoke verified status, targeted hold, and authorized response. **WORKING DRAFT — NOT FINAL-LOCKED.**

- When evidence indicates a safeguard is no longer effective, do not treat its previous verification as proof of current effectiveness.
- Update its current status to ineffective/unverified while preserving the historical verification record.
- Hold or restrict affected consequential work and use only policy-authorized containment.
- Route replacement or recovery decisions through authorized review; independently verify any replacement safeguard.
- Preserve the failure evidence, status change, containment, review, and verification history.

**Open details:** Failure-detection evidence, status labels, containment catalogue, response authority, replacement approval, and in-flight action handling.


## Working Decision 59 — Monitoring safeguard failure

**Selected direction:** C — Risk-based monitoring plus change-triggered checks. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Monitoring strength and frequency should reflect risk and failure impact.
- Use scheduled checks, available event/change signals, and re-verification when relevant changes occur.
- Make monitoring gaps, stale evidence, and coverage limitations visible.
- Do not silently treat a safeguard as verified when current monitoring coverage or evidence is uncertain.
- Preserve monitoring observations, verification outcomes, gaps, and resulting actions.

**Open details:** Risk-to-monitoring mapping, check intervals, available event signals, freshness thresholds, gap alerts, and behavior when monitoring is unavailable.


## Working Decision 60 — Safeguard monitoring becomes unavailable

**Selected direction:** C — Monitoring-degraded state plus risk-based restrictions. **WORKING DRAFT — NOT FINAL-LOCKED.**

- Explicitly show when safeguard monitoring is unavailable and current effectiveness cannot be confirmed.
- Apply risk-proportionate restrictions or holds to affected consequential actions.
- Use alternative verification only when explicitly authorized by policy.
- When monitoring is restored, reconcile available evidence and revalidate the safeguard's current status.
- Preserve outage periods, coverage gaps, alternative checks, evidence, and status transitions.

**Open details:** Degraded-state labels, risk-to-restriction mapping, approved alternatives, outage thresholds, reconciliation procedure, and alert/escalation rules.


## Working Decision 61 — Evidence gaps after monitoring restoration

**Selected direction:** C — Reconcile available evidence plus explicit gap assessment. **WORKING DRAFT — NOT FINAL-LOCKED.**

- After monitoring returns, reconcile available authoritative evidence and document the outage interval, missing evidence, and remaining uncertainty.
- Perform risk-based revalidation; monitoring restoration alone does not restore verified status.
- If the gap remains material or unresolved, maintain applicable holds/restrictions on affected consequential work.
- Preserve evidence provenance, reconciliation results, gap assessment, and status transitions.

**Open details:** Materiality thresholds for evidence gaps, authoritative sources, reconstruction limits, risk-to-revalidation mapping, and release criteria.

## Decision 62 — Materiality of Evidence Gaps (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Determine whether an evidence gap is material using policy-defined criteria and conservative uncertainty handling. The criteria should consider the risk of the affected work, the duration and extent of the missing interval, the criticality of the missing evidence, and the plausible impact of proceeding without it. AI may explain the evidence available, the gap, and the likely implications, but it must not invent or override materiality rules. If criteria are missing, ambiguous, or do not cover the case, keep affected consequential work held or restricted and require authorized review. Monitoring restoration alone does not establish that a gap is immaterial.

## Decision 63 — Ownership and Versioning of Evidence-Gap Materiality Criteria (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

A designated, authorized policy owner approves and versions the evidence-gap materiality criteria. Record each change's rationale, effective date, version, and assessed impact. Apply the currently applicable approved version when assessing a gap. If ownership, applicability, or criteria are unclear, do not let AI invent a rule; keep affected consequential work held/restricted pending authorized review. Preserve the prior versions and change history for traceability.

## Decision 64 — Handling Existing Assessments After Materiality-Criteria Changes (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use impact-based reassessment when materiality criteria change. Identify which existing assessments may be affected and reassess those against the currently applicable approved criteria version. Preserve prior assessments and their criteria/version history. Apply proportionate holds or restrictions to affected consequential work while material impact is being determined or required reassessment remains incomplete. Do not assume an old assessment remains applicable solely because it was valid under an earlier version; do not indiscriminately reassess unrelated assessments without an identified impact basis.

## Decision 65 — Status While Reassessment Is Pending (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

When a criteria change triggers reassessment, preserve the previous verified result as historical evidence, but show the current status as `REASSESSMENT PENDING` (or an equivalent explicit uncertainty state). Apply proportionate restrictions to affected consequential work while reassessment is pending. After reassessment, update current status using the applicable approved criteria version and available evidence. Do not silently treat the historical result as current verification, and do not automatically label every affected assessment INVALID solely because criteria changed.

## Decision 66 — Handling Overdue Reassessment (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Set policy-defined, risk-based deadlines for reassessment review. If a deadline is missed, retain the pending status and applicable restrictions and trigger authorized review/escalation under the approved process. A missed deadline does not constitute approval, resolution, acceptance of uncertainty, or automatic restoration of the prior status. Preserve the deadline, missed-deadline event, escalation attempts/outcomes, current restrictions, and eventual disposition in the history.

## Decision 67 — Escalation With No Response (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

When policy-defined escalation levels and attempts are exhausted without a response, persistently mark the issue as unresolved. Maintain applicable restrictions. Only explicitly permitted monitoring and safe, independent work may continue. Further consequential action requires an authorized decision and required verification. Record escalation levels/attempts, delivery or response status where known, the unresolved disposition, continuing restrictions, and subsequent authorized decisions. Silence or exhaustion of the escalation path is not approval or resolution.

## Decision 68 — Review Triggers for Persistently Unresolved Issues (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use risk-based periodic review deadlines for persistently unresolved issues, and trigger an earlier review when relevant new evidence, policy changes, authority availability, or issue-state changes occur. Record the trigger, evidence/state considered, review outcome, and any remaining uncertainty. A review trigger or completed review does not by itself lift restrictions or authorize consequential work; applicable release conditions and authorized decisions remain required.

## Decision 69 — Handling an Issue That Remains Unresolved After Review (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

After review, if uncertainty remains unresolved, record an explicit unresolved disposition, including remaining evidence gaps, checks attempted, and open questions. Maintain applicable holds and restrictions. Set the next review or escalation according to policy. Closure requires an authorized decision and the required verification; repeated unsuccessful reviews do not automatically close the issue.

## Decision 70 — Scheduling the Next Review for Unresolved Issues (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use policy-defined, risk-based default deadlines for reviewing unresolved issues. An authorized reviewer may approve an earlier or later review date with a documented reason only where policy permits. Missing an overdue review does not close the issue or automatically lift restrictions; applicable holds and restrictions remain until the required authorized disposition and verification.

## Decision 71 — Handling a Missed Review Deadline (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record and visibly maintain overdue status. Trigger risk-based escalation according to policy. If escalation is unavailable, applicable holds and restrictions remain in place. Missing the deadline is not approval, resolution, or authorization to release a hold.

## Decision 72 — Handling a Response from an Authorized Reviewer After Escalation (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Validate the reviewer's authority and decision scope. Record the response, rationale, and any unresolved uncertainty. Update the issue status or restrictions only after required evidence and verification are complete. Receipt of a response alone does not release a hold or resolve the issue.

## Decision 73 — Handling an Unclear or Incomplete Reviewer Decision (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Mark an unclear or incomplete reviewer response as `AMBIGUOUS / INCOMPLETE`. Record what is missing or conflicting and request clarification from an authorized reviewer. Applicable holds and restrictions remain in place. The AI must not infer missing authorization or intent.

## Decision 74 — No Response to a Clarification Request (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use policy-defined bounded follow-up attempts and record delivery and request status. Once the permitted limit is reached, use an authorized escalation route. If no response arrives, the ambiguity and applicable holds/restrictions remain. Silence is not approval.

## Decision 75 — No Clarification After Escalation (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

When policy-defined escalation routes and attempts are exhausted without clarification, set status to `ESCALATION EXHAUSTED — UNRESOLVED`. Record attempts and the missing response, maintain applicable restrictions, and set the next review according to policy. No automatic approval, closure, or hold release is permitted.

## Decision 76 — Ownership After Escalation Is Exhausted (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Retain the existing authorized owner in the record. If the owner is unavailable or transfer is necessary, formally assign the issue only to an alternate authorized under policy. If no valid alternate exists, mark `OWNER UNAVAILABLE`, maintain applicable restrictions, and preserve review status. The AI must not independently transfer ownership or authority.

## Decision 77 — Process After `OWNER UNAVAILABLE` (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Follow the policy-defined timeframe and authorized escalation path. Assign an owner only through a pre-authorized alternate or a formally authorized leadership decision. If no valid assignment is available, retain `OWNER UNAVAILABLE` and applicable restrictions, and set the next review according to policy. Notification or silence does not transfer authority.

## Decision 78 — Undefined Escalation Timeframe (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Do not invent an approved escalation timeframe when policy does not define one. Request clarification or a policy update from a policy-authorized owner. Until resolved, record `ESCALATION TIMING UNDEFINED`, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. AI must not create a deadline or authority independently.

## Decision 79 — Review Timing While a Policy Gap Remains Unresolved (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Set review timing only from an applicable existing policy or a formally authorized reviewer's approved direction, where policy permits. If no valid schedule is defined, record `REVIEW TIMING UNDEFINED`, request clarification, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. AI must not create a deadline independently.

## Decision 80 — No Response to Policy Clarification Request (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use only policy-defined follow-ups, deadlines, and escalation routes. If policy does not define these either, record the policy gap and request direction from an authorized policy owner. Until valid direction is received, preserve the undefined status and applicable restrictions. AI must not create a fallback policy.

## Decision 81 — Policy Owner Also Unavailable (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use only an alternate governance contact or decision path that is already defined and authorized by policy. If no such path exists, record `POLICY GOVERNANCE UNAVAILABLE`, maintain applicable restrictions, and continue only explicitly permitted safe monitoring. AI must not appoint alternate authority or independently resolve the policy gap.

## Decision 82 — Review Trigger When Governance Is Unavailable (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use only review triggers and timing defined by policy. If these are undefined, record `GOVERNANCE REVIEW TRIGGER UNDEFINED` and request authorized governance clarification. Relevant new information may be recorded, but it does not automatically change authority or restrictions. AI must not create a review schedule or governance authority independently.

## Decision 83 — New Critical Information During a Governance Gap (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Preserve the information and evidence, assess urgency against available approved criteria, and apply only policy-authorized protective measures. If criteria or authority are unclear, record `CRITICAL INFORMATION — GOVERNANCE REVIEW REQUIRED`, maintain applicable restrictions, and continue only explicitly permitted safe containment or monitoring. AI must not expand its authority independently.

## Decision 84 — Critical Information Notification During a Governance Gap (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use only policy-authorized recipients and approved channels. If a recipient or channel is undefined or unavailable, record `CRITICAL NOTIFICATION PATH UNAVAILABLE`, track delivery status accurately, and maintain applicable restrictions. AI must not invent recipients, channels, or escalation authority.

## Decision 85 — Failed Critical Notification Delivery (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use only policy-authorized retry limits and explicitly approved fallback channels. Clearly record delivery failure or uncertainty. If no authorized route is available, set `CRITICAL NOTIFICATION DELIVERY UNRESOLVED`, maintain applicable restrictions, and continue only permitted safe monitoring or containment. AI must not independently switch channels or invent escalation authority.

## Decision 86 — Delivered Notification Without Recipient Acknowledgement (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Track delivery and acknowledgement as separate statuses. Follow only policy-defined acknowledgement deadlines, reminder limits, and escalation routes. If a deadline is missed, apply policy-defined escalation and status updates. Lack of acknowledgement is not resolution or approval. If rules are undefined, record the policy gap, maintain applicable restrictions, and do not let AI invent deadlines or escalation authority.

## Decision 87 — Acknowledgement Without Issue Resolution (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record acknowledgement as a communication event while maintaining issue status independently. Acknowledgement does not close the issue, release restrictions, grant approval, or transfer ownership. Further follow-up must use the applicable policy and authorized workflow.

## Decision 88 — Closure Verification Fails or Is Unavailable (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Use policy-required verification for closure. If verification succeeds, follow the authorized closure process. If verification fails, conflicts, or is unavailable, set `CLOSURE UNVERIFIED`, record the evidence gap, and maintain applicable restrictions. Only policy-authorized review or recovery steps are permitted. AI must not invent verification or closure criteria.

## Decision 89 — Contradictory Evidence After Successful Closure Verification (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Preserve contradictory evidence with its source and timestamp. Assess relevance and reliability under applicable policy. While impact is unclear, set `CLOSURE UNDER REVIEW` and maintain necessary restrictions. Reopen, retain closure, or investigate further only through the authorized policy-defined process; AI must not independently reverse or confirm closure.

## Decision 90 — Closure After Review of Contradictory Evidence (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Preserve the evidence resolving the contradiction and the reviewer disposition. Closure requires satisfying applicable policy criteria and required verification, including independent verification wherever policy requires it. If any criterion is missing, conflicting, or unverifiable, maintain `CLOSURE UNVERIFIED` and applicable restrictions. Reviewer confirmation alone does not substitute for missing evidence.

## Decision 91 — Incomplete or Unavailable Audit Record After Formal Closure (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Explicitly record missing or unavailable audit evidence and preserve available records and provenance. Set `CLOSURE AUDIT REVIEW REQUIRED`. Assess under applicable policy whether closure remains valid or further review is needed. Maintain necessary restrictions. AI must not fabricate missing audit entries or independently reopen or re-close the issue.

## Decision 92 — Audit Record Repaired but Completeness Unverified (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record repaired entries, their source, repair method, and confidence/verification status separately. Distinguish original evidence from reconstructed information. If completeness cannot be verified, set `AUDIT COMPLETENESS UNVERIFIED` and maintain applicable restrictions. Update review status only after policy-defined criteria and authorized review. AI must not invent missing history.

## Decision 93 — Clearing Audit Review After Completeness Is Verified (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Preserve evidence and provenance for audit-completeness verification. Clear audit-review status only after applicable policy criteria are satisfied and an authorized review approves clearance. Validate closure status separately; verified audit completeness does not by itself validate issue closure. If criteria are missing or uncertainty remains, maintain review status and applicable restrictions.

## Decision 94 — Audit Review Cleared but Issue Closure Validation Fails (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Keep audit-review clearance and issue-closure validity as independent states. If closure validation fails or is unavailable, record `ISSUE CLOSURE VALIDATION FAILED` or `ISSUE CLOSURE VALIDATION UNAVAILABLE`, preserve evidence, and maintain applicable restrictions. Reopening, retaining closure, or further review must follow the authorized policy-defined process. Clearing audit review does not automatically close or reopen the issue.

## Decision 95 — Reviewer Unavailable After Closure Validation Fails (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record closure-validation failure/unavailability and keep the issue in an unresolved review state. Use only a policy-designated alternate reviewer or explicitly authorized governance route. If no such route is available, set `CLOSURE REVIEWER UNAVAILABLE`, maintain applicable restrictions, and continue only permitted safe monitoring/containment. AI must not appoint a reviewer, reverse closure, or transfer authority on its own.

## Decision 96 — Resume Review When Authorized Reviewer Returns (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Before resuming, revalidate the reviewer's current identity, authority, and scope. Recheck the latest issue status, closure-validation evidence, audit trail, applicable policy, and intervening changes. Verified prior work may be reused, but stale, missing, or conflicting evidence remains unresolved. Resume only within the validated scope; if required checks are incomplete, maintain the current status and applicable restrictions.

## Decision 97 — Missing or Conflicting Evidence When Review Resumes (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Explicitly identify and record missing/conflicting evidence, preserving provenance and timestamps. Perform bounded verification only through policy-approved authoritative sources. Keep the affected closure determination unresolved and maintain applicable restrictions. Unaffected review work may continue only when demonstrably safe and independent. If the gap remains unresolved, use the authorized policy-defined review/escalation route. AI must not independently choose evidence to settle a conflict.

## Decision 98 — Evidence Conflict Remains Unresolved After Authorized Review (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record `EVIDENCE CONFLICT UNRESOLVED` as the disposition. Preserve conflicting evidence, provenance, checks attempted, and remaining uncertainty. Maintain applicable restrictions. If policy permits, explicitly authorized residual-risk acceptance or bounded safe continuation may be considered; otherwise affected consequential work remains held. Future review/change triggers must be policy-defined. Completing a review does not by itself close the issue or release a hold.

## Decision 99 — Residual-Risk Acceptance Expires or Becomes Invalid While Evidence Conflict Remains Unresolved (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Do not treat expired or invalid residual-risk acceptance as current authorization. Obtain a fresh assessment/acceptance from an authorized reviewer as required by applicable policy. Keep affected consequential work held or restricted; safe independent work may continue only where policy explicitly permits. Preserve the prior acceptance, expiry/invalidity reason, current evidence, and reassessment history. Expiry does not itself close the issue or accept residual risk.

## Decision 100 — Restrictions After Fresh Residual-Risk Acceptance (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record and validate the fresh residual-risk acceptance, then revalidate current evidence, authority, safeguards, and policy-required checks. Lift only those restrictions whose release prerequisites have passed and whose release is permitted by policy. If evidence or checks are missing, conflicting, or uncertain, affected restrictions remain in place. Acceptance approval alone is not proof that restrictions may be lifted.

## Decision 101 — Resume Work After Restrictions Are Lifted (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Before resuming, revalidate current state, action status, authority, approval validity, applicable policy, and dependencies. Resume only work that is verified, still authorized, and within the released scope. Material changes require fresh approval. Unknown state remains paused. Verify and record the outcome of every resumed action.

## Decision 102 — Resumed Action Fails or Outcome Is Unknown (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

On failure or unknown outcome, first reconcile actual state using approved, bounded read-only checks. Do not assume unknown means success or failure; do not blindly retry, roll back, or repair. Pause the affected action and dependent work. Demonstrably safe independent work may continue. Retry or recovery is allowed only after explicit policy authorization and verification of prerequisites.

## Decision 103 — Handling a Confirmed Action Failure After Reconciliation (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Record the confirmed failure and assess the affected workflow. Retry only when policy explicitly permits retry, duplicate-side-effect risk has been assessed, current state and prerequisites are verified, and required authorization remains valid. Otherwise pause and present recovery options and evidence to the user/authorized reviewer. Confirmed failure alone does not authorize a retry.

## Decision 104 — Retry Limits for Eligible Failed Actions (Option C)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Retry count, timing, and backoff are defined by an approved action-specific policy. In the MVP, if a configured limit is missing, retry safety is unclear, or the outcome is uncertain, do not automatically retry; pause and ask. Record every attempt in the audit trail. When the limit is exhausted, follow the authorized recovery path.

## Decision 105 — First MVP Implementation Target (Option A)
**Status:** WORKING DRAFT — NOT FINAL-LOCKED

Start implementation with a Context Note Update as the first end-to-end action. Use an illustrative project note update with explicit authorization, policy/risk checks, durable audit before dispatch, expected-version checking, preservation of the prior version, read-back verification, and safe unknown-outcome reconciliation. The illustrative example `Budget assumption: ₹0` to `Budget assumption: ₹500` is not authorization to change a real note. Validate the action contract and failure paths before expanding the action catalogue.

## Project Bible v1.0 — Development Baseline Approval
**Approval status:** APPROVED AS DEVELOPMENT BASELINE  
**Scope:** Decisions 1–105 and current documented product direction are the baseline for implementation. This approval does not certify every decision as production-ready or close unresolved implementation, security, policy, or governance blockers. Individual decisions and technical details remain subject to their documented working-draft status and required validation.

Baseline governance:
- Implement against the approved MVP scope unless a documented blocker requires a change.
- Keep unresolved blockers visible and open until explicitly resolved and verified.
- Record material scope/contract changes with rationale, approval, and affected components.
- Do not treat baseline approval as authorization to perform real-world actions or bypass safety gates.

