# 06 — USER FLOWS

## Status
WORKING DRAFT — main product flow discussed; detailed screen/API behavior remains undecided.

## Main Flow

```text
User records a project decision
  ↓
AI drafts decision, reason, assumptions, evidence, date, related project/goal
  ↓
User reviews and confirms the record
  ↓
AI suggests important assumptions to monitor
  ↓
User selects assumptions to monitor
  ↓
System checks for relevant changes and evidence
  ↓
System verifies a change and connects it to affected decisions
  ↓
AI explains impact and researches alternatives/recovery
  ↓
Permission policy determines whether approval is required
  ↓
Approved action is performed when allowed
  ↓
System verifies the result and updates relevant context
```

## Flow: Decision Timeline
1. Relevant project events are automatically recorded.
2. Timeline displays events chronologically.
3. User can filter by event type.
4. User can add personal notes or correct entries.
5. Corrected information is shown as current while correction history is retained.
6. User can delete an entry and undo within 30 seconds.

## Permission Flow
- `AUTO`: execute only inside system safety limits and the configured permissions set by authorized project administrators.
- `ASK`: explain the proposed action and impact/risks, then wait for explicit approval bound to the exact reviewed action/version, target, scope, and material parameters.
- `BLOCK`: mandatory restrictions cannot be bypassed by ordinary project permissions; other blocks may be overrideable only where policy explicitly permits it and the required authority/confirmation is recorded.
- Apply the four working risk levels `LOW / MODERATE / HIGH / CRITICAL`; exact criteria and action mappings remain undecided.
- Require risk-tiered verification. Hold consequential actions if risk, authority, required evidence, or verification is uncertain or insufficient.
- Required pre-execution evidence and audit records must be durably saved before dispatch. If persistence fails, do not execute.

## Flow: Context Note Update v0.1
1. Prepare the proposed update with the target note, current version, proposed content, and reason.
2. Check authority, permission mode, risk, required evidence, and verification plan.
3. Durably save required pre-execution audit records before dispatch.
4. If the note version changed after preparation, pause and show the current and proposed versions.
5. Ask the user how to proceed; do not silently overwrite, auto-rebase, or auto-merge.
6. After the user decides, revalidate current authorization, policy, request version, and required evidence.
7. Atomically compare the current note version with the exact approved version and apply the update only if they match; otherwise reject the write and return to the stop-and-ask conflict flow. Do not use a non-atomic check-then-write fallback.
8. Read back and verify the saved content/version.
9. Record the result; if the outcome cannot be established, mark `UNKNOWN / UNVERIFIED` and reconcile before recovery.

## Duplicate-Write Prevention Flow
1. Assign a unique Operation ID to the logical note update and associate it with the original request and audit history.
2. If the acknowledgement is lost or the save times out, mark the outcome `UNKNOWN / UNVERIFIED` unless authoritative evidence establishes the result.
3. Reconcile the original operation using an authorized, bounded method.
4. Do not automatically create a new operation or retry.
5. If status remains unknown, pause, explain known/unknown state, and ask the user how to proceed.
6. If a new write is later authorized, revalidate current authorization, policy, expected version, approval binding, and required evidence.
7. Do not claim exactly-once execution merely because an Operation ID exists.

## Save Timeout Reconciliation Flow
1. Mark the action `UNKNOWN / UNVERIFIED`; timeout alone proves neither success nor failure.
2. Perform an authorized, bounded read of the actual note state.
3. If the intended content/version is authoritatively present, verify it and record the result.
4. If the state is unchanged or ambiguous, do not automatically retry, roll back, or repair; pause and explain what is known and unknown.
5. Ask the user how to proceed if unresolved, and revalidate current authorization, policy, expected version, approval binding, and evidence before any newly authorized write.
6. Preserve the timeout, reconciliation evidence, and outcome history.

## Unknown Outcome / Recovery Flow
1. Mark the outcome `UNKNOWN / UNVERIFIED` when actual state cannot be established.
2. Preserve the last verified state and record completed, failed, in-progress, and uncertain steps separately.
3. Perform bounded, authorized reconciliation before any retry, rollback, repair, or resume.
4. Do not automatically retry, roll back, or repair.
5. If reconciliation remains inconclusive, pause affected work and explain what is known and unknown.
6. Any consequential recovery requires applicable authorization, approval, and current safety checks.
7. Append verified findings and preserve the earlier uncertainty/history.

## Unknown-Outcome Escalation — Working Decision 9
1. After timeout/lost acknowledgement, reconcile the original operation using authorized, bounded methods.
2. If authoritative evidence establishes the result, record the verified outcome and preserve timeout/reconciliation history.
3. If status remains unknown after the permitted checks, retain `UNKNOWN / UNVERIFIED`, pause affected work, and tell the user what is known and unknown.
4. Ask the user how to proceed. Do not automatically retry, create a new operation, roll back, or repair.
5. Before any later authorized write or consequential recovery, revalidate authorization, policy, expected version, approval binding, and required evidence.

## Unspecified States
Detailed loading, empty, network-error, authentication, and API-error behavior has not yet been agreed.

## User-Unavailable / Unknown-Outcome Flow
1. Keep the affected Context Note Update v0.1 action paused while its outcome remains `UNKNOWN / UNVERIFIED`.
2. If the user is unavailable, do not perform automatic further work, retry, rollback, repair, or recovery.
3. Preserve the last verified state, original Operation ID, timeout, reconciliation evidence, and uncertainty/history.
4. When the user returns, explain what is known and unknown and ask for direction.
5. Before any later action, revalidate applicable authorization, policy, expected version, approval binding, and required evidence.

## Resume Revalidation Flow
1. When the user returns and asks to continue, treat the request as direction to review/resume, not proof of the original outcome or automatic authorization to repeat a write.
2. Revalidate the original operation status, current note state/version, authorization, applicable policy, approval validity/binding, and required evidence.
3. If the operation outcome or any required state/authority/evidence remains uncertain, keep the action paused and explain what is known and unknown.
4. Do not automatically create a new operation, retry, rollback, repair, or recovery.
5. Any later write or recovery must pass the applicable current authorization, policy, version, approval, and evidence checks.

## Intended Update Already Applied Flow
1. Revalidate current note content and version against the intended update using authoritative evidence.
2. If the intended content/version is sufficiently verified, record the original operation's verified outcome and preserve timeout/reconciliation history.
3. Do not issue another write or create a replacement operation for the same intended update.
4. If content/version differs or evidence is incomplete or ambiguous, do not assume success; keep the action paused and explain known/unknown state.
5. Any separate follow-up change must pass its own applicable authorization, policy, version, approval, and evidence checks.

## Content Match / Metadata Discrepancy Flow
1. If current content appears to match the intended update but the expected version or operation metadata differs, do not mark the original operation verified based on content alone.
2. Keep the action paused and perform authorized, bounded reconciliation of authoritative note content/version and available original-operation evidence.
3. Preserve the discrepancy, original timeout, reconciliation attempts, and later findings in history.
4. If the discrepancy cannot be authoritatively explained, retain `UNKNOWN / UNVERIFIED`, explain known/unknown state, and ask the user.
5. Do not automatically write again, create a replacement operation, retry, roll back, or repair.
6. Any separate follow-up action must pass applicable authorization, policy, version, approval, and evidence checks.

## Evidence-Unavailable Uncertainty Report Flow
1. State the intended action.
2. Separate observed current state from what could not be verified.
3. Identify missing/unavailable evidence or verification sources without claiming more than the evidence supports.
4. State that no automatic new write, retry, rollback, repair, or recovery was taken.
5. Preserve the original operation, timeout, observations, reconciliation attempts, and uncertainty history.
6. Keep the outcome `UNKNOWN / UNVERIFIED` when evidence is insufficient, keep the action paused, and ask the user for direction.

## Bounded Read-Only Investigation Flow
1. Confirm that the user has authorized further investigation; authorization is limited to bounded, read-only investigation.
2. Use only permitted evidence sources to inspect and reconcile the original operation.
3. Preserve operation ID, timeout, observations, evidence, reconciliation attempts, and uncertainty history.
4. Report verified findings separately from incomplete or ambiguous evidence.
5. Do not modify the note, create a replacement operation, retry, rollback, repair, or recover as part of the investigation.
6. If the outcome remains unresolved within the boundary, retain `UNKNOWN / UNVERIFIED`, keep the action paused, and report the unresolved state.
7. Require separate authorization plus applicable policy, risk, authority, approval, and verification checks before any consequential follow-up action.

## Pre-approved Evidence Source Flow
1. Identify the evidence needed to reconcile the original operation.
2. Consult only pre-approved authoritative sources relevant to that operation.
3. Preserve observations and source/reconciliation history.
4. If an approved source is unavailable, stale, incomplete, or conflicting, do not promote an alternative source to authoritative status automatically.
5. If required verification remains unsupported, keep `UNKNOWN / UNVERIFIED`, remain paused, and report the uncertainty.
6. Keep investigation bounded and read-only; consequential follow-up requires separate authorization and applicable checks.

## Conflicting Authoritative Evidence Flow
1. Preserve each conflicting pre-approved source observation, including available timestamps and relevant provenance.
2. Do not silently prefer one source or resolve the conflict by assumption.
3. If the conflict prevents required verification, retain `UNKNOWN / UNVERIFIED`.
4. Keep the affected action paused and explain the conflicting observations and unresolved conclusion to the user.
5. Any reconciliation remains bounded and read-only and uses only pre-approved authoritative sources; no write, retry, replacement operation, rollback, repair, or recovery is performed as part of reconciliation.

## Evidence Conflict Notification Flow
1. Detect that conflicting authoritative evidence blocks required verification.
2. Keep the affected action paused and the outcome `UNKNOWN / UNVERIFIED`.
3. Notify the user promptly, explaining the conflict, known versus unresolved facts, and paused status while minimizing sensitive information.
4. Suppress duplicate alerts for the same unresolved conflict when nothing meaningful has changed.
5. Send an update when evidence or status changes meaningfully.
6. Preserve notification and status-change history.

## Notification Delivery Failure Flow
1. Record the notification attempt and observed delivery result.
2. Retry only if an explicitly defined bounded retry rule permits it; do not retry indefinitely or invent a retry policy.
3. If delivery remains unconfirmed, retain the supported failure or `UNKNOWN / UNVERIFIED` delivery status; do not claim the user received the notification without evidence.
4. Track delivery status separately from the original operation/action status.
5. Keep the affected action paused while its outcome remains unresolved.
6. Preserve notification attempts, delivery evidence, status changes, and retry history.


## Notification Delivery Evidence Flow
1. Record that the sending system accepted the notification request, if supported by evidence.
2. Separately record provider acceptance only when the provider returns supporting evidence.
3. Mark delivery confirmed only when an approved, channel-appropriate delivery signal supports that status.
4. Track user read/acknowledgement separately and only when the channel explicitly supports such evidence; do not infer it from delivery.
5. If a stage cannot be established, preserve `UNKNOWN / UNVERIFIED` for that stage and explain the limitation accurately.
6. Keep all notification lifecycle states separate from the original operation/action outcome, and preserve status transitions and supporting evidence.


## Undeliverable Notification Fallback Flow
1. Record each notification attempt and its supported result; do not claim delivery without evidence.
2. Apply only explicitly defined bounded retry rules, if any. Do not invent retries or continue indefinitely.
3. If delivery remains unconfirmed after permitted attempts, preserve failure or `UNKNOWN / UNVERIFIED` delivery status and the full attempt history.
4. Do not automatically select another channel, contact, or escalation route.
5. Keep the affected action paused while its underlying operation outcome remains unresolved; notification status does not determine operation status.
6. When the user returns, surface the pending issue and distinguish known facts, unknown delivery state, and the unresolved operation outcome.
7. Any fallback channel or escalation requires explicit authorization and applicable policy, permission, privacy, and bounded-attempt checks.


## Return-to-Session Pending Issue Flow
1. Detect a qualifying user return/session start according to the eventual approved availability/session contract.
2. If an unresolved notification-delivery issue exists, show a prominent return alert.
3. Also keep the issue visible in a persistent pending-issues panel until resolved or explicitly dismissed under applicable rules.
4. Dismissing the alert is not resolution and does not change the operation or delivery status.
5. Present known facts, unknown/unverified delivery state, and any unresolved operation outcome separately.
6. Do not resume the paused action solely because the user returned or dismissed the alert; revalidation and the applicable authorization/recovery flow still apply.


## Pending Issue Acknowledgement Flow
1. Display the unresolved issue in the persistent pending-issues panel and, when applicable, the return alert.
2. Allow the user to mark the issue as seen/acknowledged.
3. Record acknowledgement separately from delivery status, operation status, and resolution.
4. Keep the issue unresolved and visible in the panel until it is actually resolved or handled under separately approved rules.
5. Do not resume a paused action, retry, or perform recovery solely because the issue was acknowledged.
6. Apply the normal evidence, authorization, and verification requirements to resolution and any consequential follow-up.


## Shared-Project Pending Issue Acknowledgement Flow
1. An authorized project member can mark a pending issue as seen/acknowledged.
2. Record the acknowledging actor and acknowledgement event separately from issue resolution and operation/delivery status.
3. Keep the issue unresolved and visible in the persistent panel until it is actually resolved or handled under separately approved rules.
4. Acknowledgement by any project member does not authorize resumption, retry, recovery, or consequential follow-up.
5. Enforce separate permissions, evidence, and verification for resolution and consequential actions.


## Conflicting Shared-Project Instructions Flow
1. Identify the requested action and the actor's current role/permissions.
2. Check whether another active instruction or approval conflicts with the request.
3. If authority is clear and the instruction is permitted, continue through normal policy, evidence, approval, and verification checks.
4. If authority is unclear, overlapping, or conflicting, pause the affected action and request clarification through the applicable authorized process.
5. Do not treat one member's instruction as automatically overriding another's; preserve relevant instructions and decision history.
6. Revalidate authority and all relevant action state before resuming. Mandatory safety restrictions remain in force.


## Conflicting Instructions When Project Owner Is Unavailable
1. Detect the instruction conflict and determine whether the project owner is unavailable under the approved availability rules.
2. Check for a pre-designated delegate with explicit authority covering the specific conflict/action.
3. If such a delegate exists, allow resolution only within the delegate's authorized scope and normal policy/evidence/verification checks.
4. If no suitable delegate exists or authority/scope is ambiguous, keep the affected action paused and wait for the owner or another properly authorized clarification.
5. Do not infer authority from seniority, availability, or membership alone.
6. Preserve the conflict, authority basis, pause, clarification, and resolution history; revalidate before resuming.


## Delegate Expiry or Revocation Flow
1. Detect delegation expiry/revocation from an approved authoritative source.
2. Block new actions that depend on the expired/revoked delegation.
3. Pause pending affected actions and revalidate current authority before resumption.
4. For in-flight actions, safely assess actual execution state; do not blindly cancel, retry, or roll back.
5. If outcome is unknown, use approved bounded reconciliation and preserve the `UNKNOWN / UNVERIFIED` state unless sufficient authoritative evidence resolves it.
6. Require fresh authorization for consequential follow-up where applicable; preserve delegation, action, evidence, and authorization history.
7. Revalidate queued work before dispatch.


## Previously Approved Action After Delegate Revocation Flow
1. Before dispatch, detect whether the approving delegate's authority expired or was revoked after approval but before execution started.
2. Revalidate the approver identity, authority basis, approval scope/version, current policy, target, and action state.
3. Do not infer that approval is automatically valid or invalid solely from the revocation event.
4. If current authority and approval validity are established, continue only through normal dispatch checks.
5. If authority or approval validity is ambiguous, keep the action paused and request fresh authorization.
6. Preserve the original approval, revocation event, revalidation evidence, and resulting decision.


## Scope or Parameter Change After Approval Flow
1. Bind approval to the exact reviewed action, target, version, scope, and relevant parameters.
2. Before dispatch, compare the current proposed action against the approved request.
3. Apply only explicitly approved materiality rules to classify any differences.
4. For an allowed non-material change, preserve the difference and continue only if all other checks pass.
5. For a material change, revalidate and obtain fresh approval where applicable.
6. If classification or approval applicability is ambiguous, pause and request clarification; never silently reuse approval for a materially changed action.
7. Preserve original and changed requests, comparison evidence, classification, and approval history.


## Material Change Classification Flow
1. Compare the currently proposed action with the exact approved action/version/scope/parameters.
2. Use approved policy rules as the authoritative basis for materiality classification.
3. Allow AI to identify and explain differences, but not to create unsupported exceptions or override rules.
4. If an applicable rule clearly classifies the change, follow that rule and the required revalidation/approval path.
5. If rules do not cover the case or classification is ambiguous, pause and request clarification.
6. Preserve compared versions, differences, applicable rule/version, AI assistance, classification, and approval history.


## Approval Validity and Expiry Flow
1. Record the approval time, exact approved action/scope, and applicable policy-based validity rule.
2. Before execution, revalidate current authority, approved action/scope, relevant policy, and required evidence.
3. Apply risk-based validity/expiry rules; do not assume approvals last indefinitely or share one universal expiry.
4. If approval is expired or validity is ambiguous, pause and request fresh approval.
5. Preserve expiry/revalidation evidence and renewal or fresh-approval history.


## Policy Change After Approval Flow
1. Before execution, retrieve and validate the current applicable policy.
2. Compare it with the policy/version applicable when approval was granted.
3. If the update prohibits the action or materially changes approval conditions, pause and follow the required reapproval process.
4. If the change is irrelevant to the action, proceed only when documented rules permit.
5. Preserve approval-time and current policy versions, revalidation result, and any pause/reapproval history.


## Policy Change During an In-Flight Action
1. Detect and validate the updated applicable policy.
2. Assess whether it prohibits or makes continuation unsafe, using the current action state.
3. Where needed and feasible, pause/contain at a safe boundary; do not blindly cancel or assume cancellation succeeded.
4. Verify actual state before selecting next steps.
5. If policy impact or state is uncertain, hold safely where possible and request authorized review.
6. Preserve policy changes, observed state, verification/containment steps, and review history.


## Action Cannot Be Safely Paused Flow
1. Consult the pre-defined safe-stop rules for the specific action.
2. Determine whether interruption or pausing is safe and supported.
3. If safe interruption is not possible, avoid unapproved new steps and use only permitted containment.
4. Monitor and verify actual state; do not infer safety or compliance merely because the action completed.
5. Request authorized review when needed and preserve rule, policy, containment, state, verification, and review history.


## Recovery Authorization After Safe-Stop
1. Treat recovery as a separate action from the original operation.
2. Establish actual state and gather required evidence.
3. Revalidate current authority, risk, and applicable policy.
4. Obtain fresh approval from an appropriately authorized person for consequential recovery.
5. Use emergency permissions only when explicitly defined by policy and within scope.
6. Preserve rationale, state/evidence, authority/approval, execution, and verification history.


## Recovery Approver Unavailable Flow
1. Establish the current action state and determine that recovery approval is required.
2. Check for a pre-designated alternate/emergency role with explicit authority for this recovery scope.
3. Revalidate role authority, policy, scope, and current action state before proceeding.
4. If a valid path exists, request/obtain authorization through that path.
5. If no valid path exists, safely hold where possible, monitor state, and escalate; do not infer or transfer authority automatically.
6. Preserve availability status, escalation path, authority checks, evidence, and decision history.


## Emergency Recovery Authority Flow
1. Confirm that the emergency role is explicitly designated and currently authorized.
2. Check the requested recovery action against the predefined action catalogue, scope, risk limits, and expiry.
3. Perform required state/evidence checks and record the authorization and action.
4. If any limit is exceeded or authority is ambiguous, do not execute; seek fresh authorization or safely hold.
5. Preserve the action and evidence for audit and post-action review.


## Post-Emergency-Recovery Review Flow
1. Record the emergency recovery rationale, authorization, and execution.
2. Verify the actual outcome using the required evidence source.
3. Determine whether risk or exception criteria require independent human review.
4. Keep the issue unresolved until required verification and review checks are complete.
5. Record reviewer decision, remaining risk, and closure status; do not infer closure from apparent success alone.


## Residual-Risk Acceptance Flow
1. Verify the recovery outcome and identify remaining risk, evidence, and uncertainty.
2. Determine the required acceptance authority from current policy and risk level.
3. Present the residual risk and supporting evidence to an authorized person; AI may explain but cannot accept on their behalf.
4. Record explicit acceptance or rejection, authority, rationale, scope, and any expiry/review conditions.
5. Do not use acceptance to bypass prohibited or unbounded risk; close only when applicable closure criteria are satisfied.


## Accepted Residual-Risk Review Flow
1. Record the accepted residual risk, authorized acceptor, scope, evidence, and any review conditions.
2. Set the next review according to the risk level and applicable policy.
3. Watch for relevant changes in policy, evidence, environment, or assumptions that trigger earlier reassessment.
4. If acceptance expires or becomes invalid, do not treat it as current approval; pause affected consequential work and route for authorized reassessment as policy requires.
5. Preserve the review outcome and history; reassessment does not itself imply the risk is accepted again.


## Increased Residual-Risk Flow
1. Detect a possible increase in residual risk and preserve the signal, provenance, and uncertainty.
2. Verify evidence and determine whether the increase is material under policy.
3. If material or unresolved at a consequential-action boundary, apply proportionate temporary safeguards and pause/contain affected actions where needed.
4. Route the updated risk for authorized reassessment; the AI explains evidence and options but does not accept the risk.
5. Record the reassessment and safeguard outcome. Do not treat a signal alone as conclusive proof or silently carry forward an acceptance that may no longer apply.


## Pending Risk Reassessment Flow
1. Identify actions and dependencies potentially affected by the changed risk.
2. Apply a targeted hold or restriction to affected consequential actions, proportionate to risk and uncertainty.
3. Continue only work demonstrably safe and independent of the unresolved risk.
4. Show the unresolved status, affected scope, restrictions, and next required review to the user.
5. Reassess with authorized evidence and authority before lifting restrictions; do not interpret elapsed time or project activity as approval.


## Risk-Hold Release Flow
1. Identify the hold, affected scope, current risk level, and release prerequisites.
2. Verify the required evidence and safety conditions against current policy.
3. Route the release decision to a person/role authorized for that risk level.
4. Do not let AI alone release a consequential-action hold or use approval to bypass prohibited risks.
5. Record the evidence, authority, decision, timestamp, scope, and any conditions; only then update the hold status.


## Post-Hold-Release Verification Flow
1. Confirm the authorized hold-release decision and its exact scope.
2. Revalidate current state, authority, applicable policy, evidence, and prerequisites for affected actions and dependencies.
3. Resume only the verified scope; keep unknown or failed checks on hold.
4. Record checks, evidence, scope, release authority, and resume outcome.
5. Treat partial/in-flight work according to its actual reconciled state; do not infer safety from the release approval alone.


## Failed Verification After Hold Release
1. Restore or maintain the hold for actions whose required checks failed or remain unknown.
2. Record which checks failed, the available evidence, and any uncertainty.
3. Permit only bounded diagnosis explicitly allowed by current policy; do not blindly retry, rollback, or repair.
4. Continue verified unaffected work only when demonstrably safe and independent of the held scope.
5. After remediation, follow the authorized hold-release and pre-resume verification flow again; diagnosis or remediation alone does not lift the hold.


## Unresolved Diagnosis and Escalation Flow
1. Continue only bounded investigation permitted by current policy, with explicit time, budget, and evidence limits.
2. If unresolved at a limit—or if risk requires earlier escalation—route to the appropriate risk-level authority.
3. Keep the affected scope held and safeguards active while unresolved.
4. Record investigation steps, evidence, limits reached, escalation target, delivery/acknowledgement state, and response.
5. Do not treat handoff, notification, or elapsed time as resolution; follow the authorized reassessment and release flow after remediation.


## Escalation Recipient Unavailable Flow
1. Record the escalation attempt, intended recipient, delivery/acknowledgement state, and applicable response deadline.
2. Follow the policy-defined alternate escalation chain and verify that the alternate is authorized for the required decision.
3. If no valid alternate is available, keep the affected scope held and continue only explicitly permitted monitoring.
4. Display the unresolved escalation and lack of response; do not grant AI or another person authority by default.
5. Preserve the escalation history and resume only through the applicable authorized reassessment and release flow.


## Missed Escalation Deadline Flow
1. Check the policy-defined response deadline and the recorded delivery/acknowledgement state.
2. If the deadline has passed without a response, escalate to the next eligible authority in the approved ladder.
3. Record each attempt, recipient, timestamp, delivery/acknowledgement state, and current overdue status.
4. If no valid authority responds, keep the affected scope held and continue only permitted monitoring.
5. Do not treat timeout, silence, or repeated notifications as approval or resolution; use the authorized reassessment/release flow when a valid decision is received.


## All Escalation Levels Exhausted Flow
1. Confirm that each policy-defined escalation level was attempted and record its delivery/acknowledgement state.
2. Mark escalation as exhausted and preserve the issue as unresolved; do not infer approval from silence.
3. Record the responsible owner and next review condition when policy provides them.
4. Keep affected scope held and continue only explicitly permitted monitoring.
5. Resume only after a valid authorized decision, required verification, and the applicable hold-release process.


## Review Flow After Escalation Is Exhausted
1. Keep the issue visibly unresolved and the affected scope held.
2. Schedule review according to risk and policy-defined intervals.
3. Trigger earlier review when relevant evidence, policy, authority availability, or issue state materially changes.
4. Reassess using current authoritative evidence and preserve the review history.
5. Treat a triggered review as reassessment only; do not release the hold without the applicable authorized decision and required verification.


## Conflicting Evidence During Review Flow
1. Preserve each conflicting evidence item, source, timestamp, provenance, and observed discrepancy.
2. Use only approved authoritative sources for bounded verification; do not silently rank evidence by recency alone.
3. If the conflict remains unresolved, mark the result uncertain and keep affected scope held.
4. Record verification attempts, limits, and unresolved questions; escalate through the authorized process when required.
5. Release the hold only after applicable authorized reassessment and required verification.


## Authorized Review of Unresolved Evidence Conflict
1. Preserve conflicting evidence, timestamps, provenance, and bounded verification attempts.
2. Have AI summarize the conflict, evidence limitations, and potential impact without silently choosing a winner.
3. Route to a reviewer authorized under the applicable risk policy.
4. The reviewer records whether evidence is sufficient, more verification is required, or uncertainty may be accepted under policy.
5. Keep the affected scope held unless the resulting decision satisfies all applicable authorization, policy, and verification requirements.


## Reviewer Cannot Resolve Uncertainty Flow
1. Preserve the reviewer's findings, evidence considered, gaps, and remaining uncertainty.
2. Record an explicit unresolved disposition; do not force an unsupported conclusion or mark the issue resolved.
3. Maintain applicable hold and safeguards on the affected scope.
4. Schedule risk-based review and trigger earlier reassessment when relevant new evidence arrives.
5. Release the hold only through the applicable authorized decision and required verification process.


## Accepting Unresolved Uncertainty Flow
1. Identify the unresolved uncertainty, evidence gaps, affected scope, and current safeguards.
2. Check whether policy permits residual-risk acceptance for this issue and risk level.
3. If permitted, obtain explicit acceptance from an authorized person; record rationale, scope, evidence, and validity conditions.
4. Continue only demonstrably safe work independent of the unresolved issue.
5. Keep prohibited actions blocked; reassess when acceptance expires or relevant conditions/evidence change.
6. Acceptance alone does not release holds or bypass separate verification and authorization requirements.


## Changed Accepted-Risk Flow
1. Detect a relevant change in evidence, policy, environment, or assumptions.
2. Assess whether the change may materially affect previously accepted residual risk.
3. Revalidate the risk and apply proportionate safeguards; restrict or hold affected consequential actions when needed.
4. Continue only demonstrably safe work independent of the affected risk.
5. Record the change, evidence, reassessment, safeguards, authority, and outcome.
6. Do not treat prior acceptance as automatically valid after a material change; follow applicable authorization and verification before lifting holds.


## Temporary Safeguards During Risk Reassessment Flow
1. Identify the risk change and affected scope.
2. Match the situation against the current pre-approved safeguard policy.
3. Apply only safeguards that are explicitly authorized and covered by the policy.
4. If no rule covers the case or interpretation is ambiguous, hold affected consequential work and request authorized review.
5. Record the trigger, policy rule, safeguard, scope, and escalation history.
6. Keep the underlying risk unresolved until the applicable reassessment, authorization, and verification requirements are met.


## Temporary Safeguard Effectiveness Verification Flow
1. Apply a policy-authorized temporary safeguard and record the action.
2. Verify its actual effect using policy-defined authoritative evidence and risk-appropriate checks.
3. Do not treat command success alone as proof of effectiveness.
4. If evidence is missing, conflicting, stale, or uncertain, leave the safeguard unverified.
5. Maintain the affected consequential-work hold and request authorized review/escalation.
6. Record evidence, result, uncertainty, and subsequent review; do not lift the hold solely because the safeguard command succeeded.


## Previously Verified Safeguard Becomes Ineffective Flow
1. Detect or receive credible evidence that a previously verified safeguard may no longer be effective.
2. Preserve the historical verification record but update current status to ineffective/unverified.
3. Hold or restrict affected consequential work and apply only policy-authorized containment.
4. Request authorized review for replacement safeguard or recovery decisions.
5. Independently verify any replacement safeguard before treating it as effective.
6. Record failure evidence, status change, containment, authorization, and verification history.


## Safeguard Failure Monitoring Flow
1. Determine the safeguard's risk level and the impact if it fails.
2. Configure policy-authorized scheduled checks and relevant available event/change triggers.
3. Re-verify when a relevant change or failure signal occurs.
4. Display monitoring coverage gaps, stale evidence, and unavailable checks.
5. If current effectiveness cannot be established, do not silently retain verified status; follow the applicable hold and review process.
6. Preserve observations, evidence, gaps, and follow-up actions.


## Safeguard Monitoring Unavailable Flow
1. Detect monitoring unavailability and mark monitoring coverage as degraded.
2. Make the outage, evidence gap, and uncertainty about current safeguard effectiveness visible.
3. Apply risk-based restrictions/holds to affected consequential actions.
4. Use an alternative verification method only if policy explicitly authorizes it.
5. When monitoring returns, reconcile evidence gathered during the gap and revalidate current status.
6. Preserve outage, gap, alternative-check, evidence, and status-transition history.


## Evidence Gap After Monitoring Restoration Flow
1. Confirm monitoring has returned, without assuming safeguard effectiveness is restored.
2. Reconcile available authoritative evidence for the outage interval.
3. Document the missing interval, unavailable evidence, source provenance, and remaining uncertainty.
4. Revalidate current safeguard status according to risk and applicable policy.
5. If a material gap remains unresolved, keep affected consequential work restricted/held.
6. Preserve reconciliation findings, gap assessment, and status history; restore verified status only when required evidence and checks support it.

### Decision 62 — Materiality of Evidence Gaps (working draft)
When monitoring returns, reconcile available authoritative evidence and assess any gap using policy-defined criteria: affected-work risk, missing interval, evidence criticality, and plausible impact. AI explains the gap but does not create the governing rule. Missing or ambiguous criteria require authorized review and keep affected consequential work held/restricted. Record the assessment, evidence provenance, uncertainty, and disposition.

### Decision 63 — Materiality-criteria ownership (working draft)
A designated authorized policy owner approves and versions materiality criteria. Record rationale, effective date, version, and impact for each change. Use the applicable approved version; if ownership or applicability is unclear, hold/restrict affected consequential work pending authorized review. Retain prior versions and change history.

### Decision 64 — Criteria changes and existing assessments (working draft)
On criteria change, assess impact, identify potentially affected existing assessments, and reassess affected ones against the applicable approved version. Preserve previous assessments and version history. Use proportionate holds/restrictions on affected consequential work until impact is assessed and required reassessment is complete; unrelated assessments need not be indiscriminately rerun without an impact basis.

### Decision 65 — Status during reassessment (working draft)
Preserve the prior verified result as history, but show current status as `REASSESSMENT PENDING` or equivalent uncertainty while required reassessment is incomplete. Apply proportionate restrictions to affected consequential work. Once reassessed, update current status based on applicable approved criteria and evidence; neither silently reuse historical verification nor automatically mark every affected item INVALID solely due to a criteria change.

### Decision 66 — Overdue reassessment (working draft)
Use policy-defined risk-based review deadlines. If overdue, keep reassessment pending and maintain applicable restrictions while triggering authorized review/escalation. The missed deadline is not approval, resolution, or grounds to restore the old status automatically. Record deadline, escalation, restrictions, and disposition.

### Decision 67 — No response after escalation (working draft)
After policy-defined escalation levels and attempts are exhausted without response, persistently mark the issue unresolved and maintain applicable restrictions. Permit only explicitly authorized monitoring and safe independent work. Further consequential action needs an authorized decision and required verification. Record attempts, known delivery/response status, restrictions, and later disposition; silence is not approval.

### Decision 68 — Review triggers for unresolved issues (working draft)
Schedule periodic reviews based on risk and trigger earlier review for relevant new evidence, policy changes, authority availability, or issue-state changes. Record trigger, evidence/state, outcome, and remaining uncertainty. Neither a trigger nor review completion automatically lifts restrictions or authorizes consequential work.

### Decision 69 — Unresolved after review (working draft)
If review does not resolve the issue, record the unresolved disposition, evidence gaps, checks attempted, and open questions. Keep applicable holds/restrictions in place; set next review or escalation per policy. Closure requires an authorized decision and required verification.

### Decision 70 — Risk-based next-review scheduling (working draft)
Set the next review deadline using policy-defined risk-based defaults. An authorized reviewer may approve an earlier/later date with a documented reason where policy permits. An overdue review does not close the issue or lift restrictions automatically.

### Decision 71 — Missed review deadline (working draft)
Show and record overdue status, escalate according to risk and policy, and maintain applicable restrictions if escalation is unavailable. A missed deadline is not approval, resolution, or hold release.

### Decision 72 — Authorized reviewer responds after escalation (working draft)
Validate reviewer authority and decision scope; record response, rationale, and unresolved uncertainty. Complete required evidence and verification before changing status or restrictions. A response alone is not resolution or hold release.

### Decision 73 — Unclear or incomplete reviewer decision (working draft)
Mark the response `AMBIGUOUS / INCOMPLETE`, document missing or conflicting details, and ask an authorized reviewer for clarification. Maintain applicable holds/restrictions; do not infer missing authorization or intent.

### Decision 74 — No response to clarification request (working draft)
Perform follow-ups within policy-defined limits and record delivery/status. At the limit, escalate through an authorized route. Maintain the ambiguity and applicable restrictions until properly resolved; silence is not approval.

### Decision 75 — No clarification after escalation (working draft)
When authorized escalation routes/attempts are exhausted without a response, mark `ESCALATION EXHAUSTED — UNRESOLVED`, record attempts and missing response, maintain applicable restrictions, and set the next review per policy. Do not automatically approve, close, or release the hold.

### Decision 76 — Ownership after escalation is exhausted (working draft)
Keep the existing authorized owner recorded. Transfer responsibility only through formal assignment to a policy-authorized alternate. If none is available, mark `OWNER UNAVAILABLE`, maintain applicable restrictions, and preserve review status; AI cannot transfer authority.

### Decision 77 — Process after `OWNER UNAVAILABLE` (working draft)
Follow policy-defined timing and escalation. Ownership may be assigned only to a pre-authorized alternate or through a formally authorized leadership decision. Without a valid assignment, keep `OWNER UNAVAILABLE` and applicable restrictions; schedule the next review per policy. Notification or silence is not an authority transfer.

### Decision 78 — Handling an undefined escalation timeframe (working draft)
If policy has no escalation timeframe, request clarification or a policy update from an authorized policy owner. Record `ESCALATION TIMING UNDEFINED`; keep applicable restrictions and perform only explicitly permitted safe monitoring. Do not invent a deadline or authority.

### Decision 79 — Review timing when a policy gap remains unresolved (working draft)
Use only an applicable policy-defined schedule or formally authorized reviewer direction where permitted by policy. If no valid schedule exists, record `REVIEW TIMING UNDEFINED`, request clarification, maintain applicable restrictions, and allow only explicitly permitted safe monitoring. Do not invent a deadline.

### Decision 80 — No response to policy clarification request (working draft)
Follow only policy-defined follow-ups, deadlines, and escalation routes. If those are also undefined, record the gap and seek direction from an authorized policy owner. Keep the undefined status and applicable restrictions until valid direction arrives; do not create an AI fallback policy.

### Decision 81 — Policy owner also unavailable (working draft)
Use only a policy-defined, authorized alternate governance contact or decision path. If none exists, record `POLICY GOVERNANCE UNAVAILABLE`, preserve applicable restrictions, and permit only explicitly authorized safe monitoring. AI cannot appoint an alternate authority or resolve the policy gap itself.

### Decision 82 — Review trigger when governance is unavailable (working draft)
Use policy-defined review triggers and timing only. If undefined, record `GOVERNANCE REVIEW TRIGGER UNDEFINED` and request authorized clarification. Record relevant new information without automatically changing authority or restrictions; AI cannot create review schedules or governance authority.

### Decision 83 — New critical information during a governance gap (working draft)
Preserve the information and evidence, assess urgency against approved criteria, and apply only policy-authorized protective measures. If criteria or authority are unclear, record `CRITICAL INFORMATION — GOVERNANCE REVIEW REQUIRED`, maintain applicable restrictions, and allow only explicitly permitted safe containment/monitoring. AI cannot expand its authority.

### Decision 84 — Critical information notification during a governance gap (working draft)
Notify only policy-authorized recipients through approved channels. If the recipient or channel is undefined/unavailable, record `CRITICAL NOTIFICATION PATH UNAVAILABLE`, accurately track delivery status, and maintain applicable restrictions. AI cannot invent recipients, channels, or escalation authority.

### Decision 85 — Failed critical notification delivery (working draft)
Use policy-authorized retry limits and explicitly approved fallback channels only. Record failure or uncertainty clearly. If no authorized route is available, set `CRITICAL NOTIFICATION DELIVERY UNRESOLVED`, maintain applicable restrictions, and allow only permitted safe monitoring/containment. AI cannot independently switch channels or invent escalation authority.

### Decision 86 — Delivered notification without recipient acknowledgement (working draft)
Keep delivery and acknowledgement statuses separate. Use policy-defined acknowledgement deadlines, reminder limits, and escalation routes only. Handle missed deadlines under policy; no acknowledgement is not resolution or approval. If rules are undefined, record the gap, maintain applicable restrictions, and do not invent timing or authority.

### Decision 87 — Acknowledgement without issue resolution (working draft)
Record acknowledgement as a communication event; keep the issue's status separate. Acknowledgement does not close the issue, release restrictions, grant approval, or transfer ownership. Follow up only through applicable policy and authorized workflow.

### Decision 88 — Closure verification fails or is unavailable (working draft)
Require policy-defined verification for closure. Successful verification proceeds through the authorized closure process. Failure, conflicting evidence, or unavailable verification sets `CLOSURE UNVERIFIED`; record the evidence gap and maintain applicable restrictions. Allow only policy-authorized review/recovery; AI cannot invent criteria.

### Decision 89 — Contradictory evidence after closure verification (working draft)
Preserve the evidence, source, and timestamp; assess relevance/reliability under policy. While impact remains unclear, use `CLOSURE UNDER REVIEW` and maintain necessary restrictions. Reopen, retain closure, or investigate further only through the authorized policy-defined process. AI cannot independently reverse or confirm closure.

### Decision 90 — Closure after review of contradictory evidence (working draft)
Preserve the evidence resolving the contradiction and the reviewer disposition. Close only after applicable policy criteria and required verification are satisfied, including independent verification where required. If a criterion is missing, conflicting, or unverifiable, keep `CLOSURE UNVERIFIED` and maintain applicable restrictions. Reviewer confirmation alone is not a substitute for evidence.

### Decision 91 — Incomplete or unavailable audit record after closure (working draft)
Record missing/unavailable audit evidence explicitly and preserve available records and provenance. Set `CLOSURE AUDIT REVIEW REQUIRED`; assess closure validity under applicable policy. Maintain necessary restrictions. AI cannot fabricate audit entries or independently reopen/re-close the issue.

### Decision 92 — Audit record repaired but completeness unverified (working draft)
Record repaired entries, source, repair method, and confidence/verification status; distinguish original evidence from reconstructed information. If completeness remains unverified, use `AUDIT COMPLETENESS UNVERIFIED` and maintain applicable restrictions. Update review status only under policy-defined criteria and authorized review. AI cannot invent missing history.

### Decision 93 — Clearing audit review after completeness verification (working draft)
Preserve completeness-verification evidence and provenance. Clear audit-review status only when policy criteria are satisfied and an authorized review approves clearance. Validate closure status separately; audit completeness alone does not validate issue closure. If criteria are missing or uncertainty remains, keep the review status and applicable restrictions.

### Decision 94 — Audit review cleared but closure validation fails (working draft)
Keep audit-review clearance separate from issue-closure validity. On validation failure/unavailability, record `ISSUE CLOSURE VALIDATION FAILED` or `ISSUE CLOSURE VALIDATION UNAVAILABLE`, preserve evidence, and maintain applicable restrictions. Reopen, retain closure, or further review only through the authorized policy-defined process. Audit clearance alone does not close or reopen the issue.

### Decision 95 — Reviewer unavailable after closure validation fails (working draft)
Record closure-validation failure/unavailability and retain an unresolved review state. Use only a policy-designated alternate reviewer or explicitly authorized governance route. If none is available, set `CLOSURE REVIEWER UNAVAILABLE`, maintain restrictions, and continue only permitted safe monitoring/containment. AI cannot appoint a reviewer, reverse closure, or transfer authority independently.

### Decision 96 — Resume review when authorized reviewer returns (working draft)
Revalidate reviewer identity, authority, and scope; then recheck current issue status, closure-validation evidence, audit trail, applicable policy, and intervening changes. Reuse prior work only where still verified. Mark stale, missing, or conflicting evidence unresolved. Resume only within validated scope; maintain status and restrictions while required checks remain incomplete.

### Decision 97 — Missing or conflicting evidence when review resumes (working draft)
Identify and record evidence gaps/conflicts and preserve provenance/timestamps. Use bounded verification through policy-approved authoritative sources only. Keep affected closure determination unresolved and maintain restrictions. Continue unaffected review work only if demonstrably safe and independent. If unresolved, follow authorized policy-defined review/escalation; AI cannot independently choose evidence to settle the conflict.

### Decision 98 — Evidence conflict remains unresolved after authorized review (working draft)
Record `EVIDENCE CONFLICT UNRESOLVED`; preserve conflicting evidence, provenance, checks attempted, and remaining uncertainty. Maintain applicable restrictions. Consider residual-risk acceptance or bounded safe continuation only when policy permits and the required authority explicitly approves; otherwise hold affected consequential work. Use policy-defined future review/change triggers. Review completion alone does not close the issue or release a hold.

### Decision 99 — Residual-risk acceptance expires or becomes invalid (working draft)
Do not treat expired/invalid acceptance as current authorization. Follow applicable policy for fresh assessment/acceptance by an authorized reviewer. Keep affected consequential work held/restricted; allow safe independent work only if policy explicitly permits. Preserve prior acceptance, expiry/invalidity reason, current evidence, and reassessment history. Expiry alone neither closes the issue nor accepts residual risk.

### Decision 100 — Restrictions after fresh residual-risk acceptance (working draft)
Record and validate the fresh acceptance; recheck current evidence, authority, safeguards, and policy-required checks. Lift only restrictions whose release prerequisites pass and whose release policy permits. Missing, conflicting, or uncertain evidence/checks keep affected restrictions in place. Approval alone does not prove a restriction can be lifted.

### Decision 101 — Resume work after restrictions are lifted (working draft)
Before resuming, recheck current state, action status, authority, approval validity, policy, and dependencies. Resume only verified, still-authorized work within released scope. Obtain fresh approval for material changes; keep unknown state paused. Verify and record each resumed action's outcome.

### Decision 102 — Resumed action fails or outcome is unknown (working draft)
Reconcile actual state first using approved, bounded read-only checks. Do not assume unknown means success/failure or blindly retry, roll back, or repair. Pause the affected action and dependent work; demonstrably safe independent work may continue. Retry/recovery requires explicit policy authorization and verified prerequisites.

### Decision 103 — Confirmed action failure after reconciliation (working draft)
Record the failure and assess the affected workflow. Retry only if policy explicitly permits it, duplicate side effects have been assessed, current state/prerequisites are verified, and required authorization is valid. Otherwise pause and present recovery options and evidence to the user/authorized reviewer. Failure confirmation alone is not retry permission.

### Decision 104 — Retry limits for eligible failed actions (working draft)
Use approved action-specific policy for retry count, timing, and backoff. If the MVP lacks a configured limit, retry safety is unclear, or outcome is uncertain, do not automatically retry; pause and ask. Audit every attempt. On limit exhaustion, follow the authorized recovery path.

### Decision 105 — First MVP implementation target (working draft)
Begin with a Context Note Update as the first end-to-end action: prepare the proposed note/reason and expected version; validate identity/authority and AUTO/ASK/BLOCK; assess risk; persist required audit/evidence before dispatch; perform an expected-version-checked write; preserve the previous version; read back and verify content/version; update context/timeline only after verified success. Reconcile timeouts/unknown outcomes safely; no blind retry/rollback/repair. The ₹0-to-₹500 note is illustrative, not real-action authorization.
