# 07 — FEATURE SPECIFICATIONS

## Status
WORKING DRAFT — six-feature scope is a confirmed recap of working decisions, not final-locked. Implementation details not discussed remain undecided.

### 1. Decision Memory
**Purpose:** Preserve project decisions and their rationale.
**Expected behavior:** AI drafts a record; user reviews and confirms before saving.
**Record fields:** Decision, Reason, Assumptions, Evidence, Date, Related Project/Goal.
**Out of scope:** Additional fields not explicitly agreed.

**User:** Undecided — not yet agreed.
**Trigger:** Undecided — not yet agreed.
**Inputs:** Undecided — beyond the explicitly listed behavior above.
**Outputs:** Undecided — beyond the explicitly listed behavior above.
**Dependencies:** Undecided — confirm during architecture/API review.
**API(s):** Undecided — no endpoints have been approved.
**Database entities:** Undecided — no physical schema has been approved.
**UI requirements:** Undecided — detailed UI requirements have not been agreed.
**Acceptance criteria:** Detailed, testable acceptance criteria are not final yet; do not invent additional requirements.

### 2. Decision Health Engine
**Purpose:** Represent whether a remembered decision remains suitable.
**States:** `HEALTHY`, `AT RISK`, `INVALID`.
**Working behavior:** Insufficient evidence maps to `AT RISK` while more evidence is gathered. Exact thresholds and final state definitions remain undecided.

**User:** Undecided — not yet agreed.
**Trigger:** Undecided — not yet agreed.
**Inputs:** Undecided — beyond the explicitly listed behavior above.
**Outputs:** Undecided — beyond the explicitly listed behavior above.
**Dependencies:** Undecided — confirm during architecture/API review.
**API(s):** Undecided — no endpoints have been approved.
**Database entities:** Undecided — no physical schema has been approved.
**UI requirements:** Undecided — detailed UI requirements have not been agreed.
**Acceptance criteria:** Detailed, testable acceptance criteria are not final yet; do not invent additional requirements.

### 3. Assumption Watch
**Purpose:** Monitor selected important assumptions linked to decisions.
**Expected behavior:** AI suggests assumptions; user chooses which to monitor. Maintain current project state alongside remembered decisions.
**Out of scope:** Monitoring every assumption by default.

**User:** Undecided — not yet agreed.
**Trigger:** Undecided — not yet agreed.
**Inputs:** Undecided — beyond the explicitly listed behavior above.
**Outputs:** Undecided — beyond the explicitly listed behavior above.
**Dependencies:** Undecided — confirm during architecture/API review.
**API(s):** Undecided — no endpoints have been approved.
**Database entities:** Undecided — no physical schema has been approved.
**UI requirements:** Undecided — detailed UI requirements have not been agreed.
**Acceptance criteria:** Detailed, testable acceptance criteria are not final yet; do not invent additional requirements.

### 4. Impact & Recovery Intelligence
**Purpose:** Connect relevant changes to decisions and explain the resulting impact.
**Expected behavior:** Show what changed and evidence, affected decision and reason, project impact, alternatives that meet original constraints, and a recovery plan.

**User:** Undecided — not yet agreed.
**Trigger:** Undecided — not yet agreed.
**Inputs:** Undecided — beyond the explicitly listed behavior above.
**Outputs:** Undecided — beyond the explicitly listed behavior above.
**Dependencies:** Undecided — confirm during architecture/API review.
**API(s):** Undecided — no endpoints have been approved.
**Database entities:** Undecided — no physical schema has been approved.
**UI requirements:** Undecided — detailed UI requirements have not been agreed.
**Acceptance criteria:** Detailed, testable acceptance criteria are not final yet; do not invent additional requirements.

### 5. Controlled Action
**Purpose:** Enable actions while preserving user control.

**Permission modes:** `AUTO / ASK / BLOCK`.

**Working safety direction (not final-locked):**
- Hybrid governance: system-defined mandatory safety limits; authorized project administrators configure AUTO permissions within those limits, subject to role permissions.
- Four risk levels: `LOW`, `MODERATE`, `HIGH`, `CRITICAL`; criteria, thresholds, and action mappings remain undecided.
- Risk-tiered verification; verify execution and intended outcome separately.
- Hold when risk or required verification is uncertain or insufficient.
- Unknown outcomes are `UNKNOWN / UNVERIFIED`; reconcile actual state before any retry, rollback, repair, or resume. No automatic retry, rollback, or repair; pause affected work if unresolved.
- Pre-execution evidence baseline: exact action, authority/permission evidence, safety assessment, verification plan, and durable audit record; stronger evidence for higher-risk actions.
- Approval is bound to the exact reviewed action/version, target, scope, and material parameters.
- Required audit records must be durably saved before dispatch; if persistence fails, execution stops until persistence and current checks are valid.
- Preserve attributable action history and corrections.

**Undecided:** Exact AUTO boundaries/action catalogue, permanent versus overrideable BLOCK categories and override authority, risk criteria/thresholds, action-specific evidence and verification, approval expiry/revocation, material-change rules, audit technology/retention, and implementation contracts.

**Proposed acceptance criteria:** `docs/19_implementation_readiness_review.md` (proposed, not final-approved).

**Initial action contract — Context Note Update v0.1 (working draft):**
- **Purpose:** Update one project-context note through a permission-checked, evidence-backed, verifiable action.
- **Prepare:** Identify the note, current version, proposed content, and reason.
- **Authorize and assess:** Check authority and `AUTO / ASK / BLOCK` policy; assess risk from scope, reversibility, impact, and uncertainty rather than assuming a note update is automatically LOW risk.
- **Pre-execution gate:** Persist the required action request, authority/permission evidence, safety assessment, verification plan, and durable audit record before dispatch.
- **Execute:** Apply only the authorized change against the expected note version; preserve the prior version for recovery.
- **Verify:** Read the saved note back and compare actual content/version to the intended result.
- **Conflict handling — selected A:** If the note changes after preparation, stop and ask. Show the current and proposed versions and request a decision. Do not silently overwrite, automatically rebase, or merge. Revalidate authorization, policy, request version, and evidence before resuming.

- **Pre-write version check — selected A:** Immediately before saving, compare the current note version with the exact approved version and perform the comparison plus write as one atomic conditional operation. If the version differs, reject the write, preserve the newer content, pause, and follow the stop-and-ask conflict flow. Do not silently fall back to a non-atomic check-then-write. If the storage layer cannot guarantee atomic conditional writes, hold this implementation path until a safe mechanism is selected.

- **Save timeout — selected A:** If the write outcome cannot be established after a timeout, mark `UNKNOWN / UNVERIFIED` and perform bounded, authorized reconciliation before recovery. If the intended update is authoritatively verified, record that result. If the old state or an ambiguous state is observed, do not automatically retry; pause and follow the applicable recovery/approval flow. Never automatically retry, roll back, or repair. Preserve timeout and reconciliation history, and revalidate current safety/authorization/version/approval before any newly authorized write.

- **User unavailable while outcome remains unknown — selected:** Keep the affected Context Note Update v0.1 action paused until the user returns and provides direction. No automatic further work, retry, rollback, repair, or recovery. Preserve the last verified state, original operation identity, timeout, reconciliation evidence, and uncertainty/history. On return, explain what is known and unknown and request direction; any later action must pass applicable authorization, policy, version, approval, and evidence checks.
- **Duplicate-write prevention — selected A:** Assign a unique Operation ID to each logical update and preserve it with the original request/history. After a timeout or lost acknowledgement, reconcile the original operation using an authorized, bounded method. Do not automatically create a new operation or retry. If status cannot be established, pause and ask the user. An Operation ID alone is not a guarantee of exactly-once execution; claim only guarantees the underlying mechanism actually provides.
- **Unknown-outcome escalation — Working Decision 9:** Use bounded, authorized reconciliation. If the result remains unknown, retain `UNKNOWN / UNVERIFIED`, pause affected work, tell the user what is known and unknown, and ask how to proceed. No automatic retry, new operation, rollback, or repair. Before any later authorized write/recovery, revalidate authorization, policy, expected version, approval binding, and required evidence. Exact bounds, authoritative source, notification/acknowledgement, and unresolved-case ownership remain open.
- **Unknown outcome:** Use `UNKNOWN / UNVERIFIED`; reconcile actual state before any retry, rollback, repair, or resume. No automatic retry, rollback, or repair.
- **History:** Preserve the versions, conflict, user decision, action status, and verified outcome in attributable history.
- **Example only:** Changing `Budget assumption: ₹0` to `Budget assumption: ₹500` is illustrative and does not authorize any real project edit.

This contract is a working draft. API endpoints, physical schema, exact UI, versioning/concurrency implementation, risk thresholds, and merge/rebase behavior remain undecided.


### 6. Decision Timeline
**Purpose:** Provide a chronological record of relevant project events.
**Events:** Decisions, assumption changes, decision-health changes, actions and approvals, outcomes.
**Expected behavior:** Automatic recording of relevant events; user notes and corrections; uncertain items marked unverified; corrected information shown as current while correction history is retained; chronological view with event-type filters; basic details only; delete with a 30-second undo window.
**Out of scope:** Additional timeline features not explicitly discussed.

## Cross-Feature Workflow
Decision Memory → Assumption Watch → Decision Health Engine → Impact & Recovery Intelligence → Controlled Action → verification/context update, with relevant events represented in Decision Timeline.

## API and Database Dependencies
Exact endpoints, schema, technology choices, and detailed UI requirements remain undecided. Do not invent them.

**User:** Undecided — not yet agreed.
**Trigger:** Undecided — not yet agreed.
**Inputs:** Undecided — beyond the explicitly listed behavior above.
**Outputs:** Undecided — beyond the explicitly listed behavior above.
**Dependencies:** Undecided — confirm during architecture/API review.
**API(s):** Undecided — no endpoints have been approved.
**Database entities:** Undecided — no physical schema has been approved.
**UI requirements:** Undecided — detailed UI requirements have not been agreed.
**Acceptance criteria:** Detailed, testable acceptance criteria are not final yet; do not invent additional requirements.

- **Resume revalidation after user returns — selected A:** When the user asks to continue a paused Context Note Update v0.1 operation, revalidate all relevant current state, original operation status, current note state/version, authorization, applicable policy, approval validity/binding, and required evidence. A request to continue does not prove the prior outcome or automatically authorize a repeated write. If outcome, state, authority, approval, or evidence remains uncertain, stay paused and explain known/unknown state. No automatic new operation, retry, rollback, repair, or recovery.

- **Intended note update already applied — selected A:** Verify current note content and version against the intended update using authoritative evidence. If sufficiently verified, record the original operation's verified outcome, preserve timeout/reconciliation history, and do not write again or create a replacement operation for the same intended update. If content/version differs or evidence is ambiguous/incomplete, do not assume success; remain paused and explain known/unknown state. Separate follow-up changes require their own applicable controls.

- **Content matches but version/operation metadata differs — selected:** Content match alone is insufficient to verify the original operation. Keep the action paused while performing authorized, bounded reconciliation of authoritative note content/version and available original-operation evidence. Preserve the discrepancy and history. If it cannot be authoritatively explained, retain `UNKNOWN / UNVERIFIED`, explain known/unknown state, and ask the user. No automatic write, replacement operation, retry, rollback, or repair.

- **User-facing uncertainty when evidence is unavailable — selected A:** Report the intended action, observed current state, missing/unavailable verification evidence, and actions not taken. Keep `UNKNOWN / UNVERIFIED` if evidence is insufficient, preserve operation/timeout/reconciliation history, and keep the action paused pending user direction. No automatic resume or recovery.

- **User-authorized investigation boundaries — selected A:** When an operation is `UNKNOWN / UNVERIFIED`, user-authorized investigation is bounded and read-only, using permitted evidence sources to reconcile the original operation. Preserve evidence and history; report verified versus uncertain findings. No note writes, replacement operations, retries, rollback, repair, or recovery as part of investigation. If unresolved within bounds, remain paused and `UNKNOWN / UNVERIFIED`; consequential follow-up requires separate authorization and applicable checks.

- **Evidence source authority and trust — selected A:** Context Note Update v0.1 may use only pre-approved authoritative sources relevant to the original operation. An unavailable approved source does not make an alternative source authoritative. If approved evidence is unavailable, stale, incomplete, or conflicting, retain `UNKNOWN / UNVERIFIED` unless required verification can be established from an approved source. Preserve evidence history; investigation remains bounded and read-only.

- **Conflicting authoritative evidence — selected A:** Preserve both pre-approved sources' observations, timestamps, and relevant provenance. Do not silently prefer one source or resolve by assumption. If conflict prevents required verification, retain `UNKNOWN / UNVERIFIED`, pause the affected action, and explain the conflict. Reconciliation remains bounded, read-only, and restricted to pre-approved authoritative sources.

- **User notification on evidence conflict — selected A:** Notify as soon as conflicting authoritative evidence blocks required verification and the action is paused. Suppress duplicate alerts for the same unresolved conflict without meaningful change; update on meaningful status/evidence changes. Keep `UNKNOWN / UNVERIFIED` and paused until resolved. Explain known versus unresolved facts and minimize sensitive information. Preserve notification/status history.

- **Notification delivery failure — selected A:** Record notification attempts/results accurately. Retry only under explicitly permitted bounded rules. If delivery remains unconfirmed, preserve failure or `UNKNOWN / UNVERIFIED` delivery status; never claim delivery without evidence. Track notification delivery separately from the underlying operation status. Keep the action paused while the underlying outcome remains unresolved, and preserve delivery/retry history.

- **Notification delivery evidence — selected C:** Keep sending-system request acceptance, provider acceptance, explicit delivery confirmation, and user read/acknowledgement distinct. Never infer a later lifecycle stage from an earlier one. Mark each stage only with supporting evidence; otherwise retain `UNKNOWN / UNVERIFIED` for that stage. Keep notification status separate from the underlying operation outcome and preserve status transitions/evidence. Provider, channel, receipt semantics, and freshness rules remain open.


- **Undeliverable notification fallback — selected A:** After explicitly permitted bounded attempts, if delivery remains unconfirmed, preserve failure or `UNKNOWN / UNVERIFIED` delivery status and attempt history. Do not automatically choose another channel, contact, or escalation route. Keep the affected action paused while its underlying outcome remains unresolved. Surface the pending issue clearly when the user returns. Any future fallback or escalation requires explicit authorization and applicable policy, permission, privacy, and bounded-attempt checks. User availability detection, surfacing, retention/ownership, and possible future fallback options remain open.


- **Pending issue surfacing on return — selected C:** For unresolved notification-delivery issues, show a prominent alert when the user returns/session starts and keep the issue in a persistent pending-issues panel until resolved or explicitly dismissed under applicable rules. Alert dismissal does not resolve the issue or change delivery/operation status. Returning to the app does not itself authorize resumption. Session detection, alert frequency/deduplication, panel ordering, dismissal authority/semantics, and retention/ownership remain open.


- **Pending issue acknowledgement — selected B:** User may mark an issue as seen/acknowledged; record that acknowledgement separately. The issue remains unresolved and visible in the persistent panel. Acknowledgement is not resolution, verification, or authorization to resume/retry/recover. Reversal, display, shared-project authority, and exact audit fields remain open.


- **Shared-project acknowledgement — selected C:** Any authorized project member may acknowledge a pending issue as seen. Acknowledgement is awareness only; it does not resolve/verify the issue, change operation/delivery status, or authorize resumption/retry/recovery. Resolution and consequential follow-up require separate authorization, evidence, and verification. Membership/guest boundaries, actor audit fields, and resolution role model remain open.


- **Conflicting shared-project instructions — selected C:** Role-based permissions determine who can authorize which actions. If authority is unclear, overlapping, or instructions conflict, pause the affected action and request clarification through an authorized process. One member's instruction does not automatically override another's. Safety, evidence, approval, and verification requirements remain in force. Role hierarchy, escalation path, conflict criteria, queued/in-flight handling, and pending-approval effects remain open.


- **Owner unavailable during instruction conflict — selected C:** A pre-designated delegate may resolve the conflict only when explicit authority covers the specific action and only within that scope. Otherwise, keep the affected action paused until the owner or another properly authorized person clarifies. Owner unavailability does not transfer authority automatically. Delegation/revocation, scope, expiry, unavailability criteria, escalation, and queued/in-flight effects remain open.


- **Delegate expiry/revocation — selected C:** Block new actions relying on the revoked/expired delegation; pause pending affected actions and revalidate authority before resumption. Assess in-flight state safely without blind cancel/retry/rollback. Unknown outcomes follow bounded approved reconciliation; consequential follow-up requires applicable fresh authorization. Preserve delegation/action/evidence history. Propagation timing, authoritative delegation source, in-flight classes/safe-stop, reauthorization, and queued work remain open.


- **Previously approved action after delegate revocation — selected C:** Before an approved-but-not-started action is dispatched, revalidate approver identity, authority basis, approval scope/version, current policy, target, and action state. Revocation alone does not automatically prove approval valid or invalid. Ambiguity means pause and obtain fresh authorization. Preserve approval and revocation history. Default revocation effect on delegated approvals, approval versioning, and queued approvals remain open.


- **Scope/parameter changes after approval — selected C:** Approval binds to the exact reviewed action, target, version, scope, and relevant parameters. Explicitly classified non-material changes may follow approved policy rules; material changes require revalidation and fresh approval where applicable. Ambiguity means pause and clarify. Preserve original/changed request and approval history. Action-specific materiality criteria, authority for those criteria, always-material changes, and detection/versioning remain open.


- **Material-change classification — selected C:** Approved policy rules are authoritative; AI may identify, compare, and explain differences but may not invent exceptions or override policy. Uncovered or ambiguous cases pause for clarification. Preserve compared versions, differences, rule applied, AI assistance, classification, and approval/revalidation history. Rule ownership/versioning, uncovered-case escalation, and recording of AI suggestions remain open.


- **Approval validity and expiry — selected C:** Use risk-based expiry under approved policy and revalidate authority, approved action/scope, relevant policy, and required evidence before execution. Expired or ambiguous approval means pause and obtain fresh approval. Preserve approval time, validity rule, revalidation evidence, and renewal history. Risk-to-expiry mapping, invalidating changes, clock source, notification timing, and renewal workflow remain open.


- **Policy changes after approval — selected C:** Revalidate current applicable policy before execution. If an update prohibits the action or materially changes approval conditions, pause and follow the required reapproval path. If the update is irrelevant, proceed only as documented rules permit. Preserve policy versions, revalidation results, and pause/reapproval history. Materiality/relevance criteria, authoritative version source, emergency changes, and in-flight changes remain open.


- **Policy update during an in-flight action — selected C:** Assess current policy impact and actual execution state. If continuation may be prohibited or unsafe, pause/contain at a feasible safe boundary and verify state. Do not blindly cancel, retry, roll back, or assume stopping succeeded. If uncertainty remains, safely hold where possible and request authorized review. Preserve policy version/change, state, verification/containment steps, and review history. Action-specific safe boundaries, urgent handling, continuation authority, and verification sources remain open.


- **Action cannot be safely paused — selected C:** Use action-specific safe-stop rules. If safe interruption is not possible, do not add unapproved steps; use permitted containment, monitor/verify actual state, and request authorized review. Completion alone does not prove safety or policy compliance. Preserve applicable rule, policy change, containment, observed state, verification evidence, and review history. Safe-stop catalogue, containment options, verification sources, escalation timing, and post-event recovery authority remain open.


- **Recovery authorization after safe-stop — selected C:** Recovery is a separate action; original approval does not automatically authorize it. Validate actual state, current authority, risk, and policy. Consequential recovery requires fresh approval from an appropriately authorized person. Emergency permissions are valid only when explicitly defined by policy and within scope. Preserve rationale, evidence, authorization, execution, and verification history. Recovery risk tiers, eligible roles, emergency boundaries, approval binding/expiry, and verification requirements remain open.


- **Recovery approver unavailable — selected C:** Use a pre-authorized alternate/emergency role only within explicit policy and scope. Unavailability does not automatically transfer authority to AI or another person. If no valid path exists, safely hold where possible, monitor state, and escalate without consequential recovery. Preserve availability status, path, authority checks, evidence, and recovery history. Eligible roles, scope boundaries, escalation timing, availability detection, and permitted monitoring/containment remain open.


- **Emergency recovery authority — selected C:** Narrow predefined authority with explicit action/scope/risk limits and expiry. Each recovery action requires applicable state/evidence checks and an auditable record. Outside limits, require fresh authorization or safe hold; no general discretionary emergency permission. Role eligibility, action catalogue, risk thresholds, expiry/revocation, evidence, and post-action review remain open.


- **Review after emergency recovery — selected C:** Audit every recovery and verify its outcome. Require independent human review for higher-risk or exceptional cases. Keep the issue unresolved until required checks are complete; apparent success alone does not close it. Preserve rationale, authorization, execution evidence, verification result, review decision, and closure status. Risk thresholds, reviewer eligibility/independence, evidence requirements, deadlines, and closure/residual-risk criteria remain open.


- **Residual-risk acceptance — selected C:** Policy defines who may accept each residual-risk level. AI explains risk/evidence/uncertainty but cannot accept risk itself. An authorized person explicitly accepts or rejects, and the decision is recorded. Prohibited or unbounded risks cannot be bypassed. Risk thresholds, roles, evidence, expiry/review, and closure effects remain open.


- **Review of accepted residual risk — selected C:** Review cadence is risk-based, with earlier reassessment triggered by relevant changes to policy, evidence, environment, or assumptions. Expired or invalid acceptance is not current approval. Intervals, trigger materiality, reviewer, escalation, and closure effects remain open.


- **Risk increase during reassessment — selected C:** Material risk increases trigger risk-based revalidation and proportionate temporary safeguards. Affected consequential actions may be paused or contained as needed. Verify evidence; a signal alone is not conclusive proof. Thresholds, safeguard catalogue, continuation authority, evidence standards, and in-flight handling remain open.


- **System behavior while risk reassessment is pending — selected C:** Use targeted holds/restrictions for potentially affected consequential actions. Unaffected work may continue only when demonstrably safe and independent of the unresolved risk. Display uncertainty, affected scope, and restrictions. Scope determination, safe-continuation criteria, hold ownership, status labels, escalation, and dependency handling remain open.


- **Authority to lift a risk hold — selected C:** Policy defines release authority by risk level. Required evidence and safety prerequisites must be verified. AI alone cannot release consequential-action holds, and approvals cannot bypass prohibited risks. Risk-to-role mapping, evidence, independence, emergency rules, and audit fields remain open.


- **Verification after risk-hold release — selected C:** Require scope-specific pre-resume verification of current state, authority, policy, evidence, prerequisites, and dependencies. Resume only verified scope; unknown state remains on hold. Release approval alone does not prove safe execution. Action/risk-specific checks, evidence sources, dependency boundaries, freshness, and partial-work handling remain open.


- **Verification fails after risk-hold release — selected C:** Failed/unknown checks keep the affected actions on hold. Only policy-authorized bounded diagnosis is allowed; no blind retries or unsafe rollback. Verified unaffected work may continue if safe and independent. Diagnostic bounds, retry criteria, containment, dependency scope, escalation, and re-release rules remain open.


- **Escalation when diagnosis cannot resolve the issue — selected C:** Bound investigation by policy-defined time, budget, and evidence limits; escalate unresolved issues according to risk and required authority. Affected scope stays held while unresolved. Escalation or handoff is not resolution. Risk mapping, limits, recipients, deadlines, unavailable-authority handling, and notification/status rules remain open.


- **Escalation recipient unavailable — selected C:** Use only policy-defined alternate authorities and response deadlines. If no valid alternate exists, keep affected scope held, continue permitted monitoring, and make status visible. Unavailability does not expand AI authority. Alternate-role mapping, deadlines, availability detection, monitoring limits, channels, and prolonged-unavailability handling remain open.


- **Escalation deadline missed — selected C:** Advance along a policy-authorized escalation ladder after the response deadline expires without a response. Record attempts and delivery/acknowledgement status. If no valid authority responds, keep the affected scope held and show overdue status. Silence does not mean approval or resolution. Deadline rules, ladder ownership, cadence, delivery-failure handling, overdue notifications, and prolonged non-response handling remain open.


- **All escalation levels exhausted — selected C:** Mark escalation exhausted, preserve the unresolved issue and attempt history, record the responsible owner and next review condition where policy defines them, and continue only permitted monitoring under safe hold. A valid authorized decision and required verification are necessary before release. Owner assignment, review triggers/cadence, terminal-state labels, visibility/notifications, and monitoring scope remain open.


- **Review after escalation is exhausted — selected C:** Use risk-based review cadence and trigger earlier reassessment on meaningful changes to relevant evidence, policy, authority availability, or issue state. A review trigger does not resolve the issue or release the hold. Risk intervals, materiality, trigger sources, ownership, missed-review handling, notifications, and resource limits remain open.


- **Conflicting evidence during review — selected C:** Preserve conflicting evidence with timestamps and provenance; perform bounded verification using approved authoritative sources. If unresolved, mark status uncertain and keep affected scope held. Do not prefer evidence solely because it is newer or older. Source precedence, verification limits, freshness, ownership, and notification rules remain open.


- **Unresolved evidence conflict status — selected C:** AI explains conflict and impact; a policy-authorized reviewer decides whether evidence is sufficient, further verification is needed, or uncertainty may be accepted. AI alone does not resolve consequential status. Reviewer mapping, sufficiency criteria, uncertainty acceptance, independence, expiry/review, and hold-release effects remain open.


- **Authorized reviewer cannot resolve uncertainty — selected C:** Record an explicit unresolved disposition and missing/insufficient evidence; do not force a conclusion. Maintain applicable holds and safeguards. Reassess on risk-based cadence or relevant new evidence; review alone does not release a hold. State labels, cadence, triggers, ownership, notifications, and residual-uncertainty criteria remain open.


- **Accepting unresolved uncertainty — selected C:** Permit explicit residual-risk acceptance only when policy allows and an authorized person accepts it. Record uncertainty, evidence gaps, scope, rationale, and validity conditions. Continue only demonstrably safe, independent work; prohibited/high-risk actions remain blocked. AI cannot accept risk. Policy eligibility, authority mapping, expiry/review triggers, independence criteria, and evidence requirements remain open.


- **Accepted residual risk changes later — selected C:** Relevant material changes trigger reassessment and revalidation. Apply targeted safeguards and hold/restrict affected consequential actions where needed; demonstrably safe independent work may continue. Prior acceptance is not automatically sufficient after a material change. Preserve change and decision history. Materiality criteria, thresholds, safeguards, authority, evidence freshness, and in-flight handling remain open.


- **Temporary safeguards during risk reassessment — selected C:** Use only predefined safeguards explicitly authorized by policy. If policy coverage is absent or ambiguous, hold affected consequential work and request authorized review. Safeguards do not resolve the underlying risk or independently release a hold. Preserve trigger, rule, scope, and history. Catalogue, applicability, activation authority, ambiguity thresholds, escalation timing, and in-flight handling remain open.


- **Temporary safeguard effectiveness verification — selected C:** Use policy-defined authoritative evidence and risk-based checks. Command success alone is insufficient. If verification is unavailable, conflicting, or uncertain, do not mark the safeguard verified; maintain affected consequential-work holds and escalate for authorized review. Preserve action and evidence history. Verification sources, depth, freshness, independence, and escalation timing remain open.


- **Previously verified safeguard becomes ineffective — selected C:** Revoke its current verified status while retaining historical verification. Hold/restrict affected consequential work; use only policy-authorized containment and seek authorized review for replacement/recovery. Independently verify replacements. Failure evidence and all status/response history must be preserved. Detection evidence, labels, containment catalogue, authority, replacement approval, and in-flight handling remain open.


- **Safeguard failure monitoring — selected C:** Apply risk-based monitoring using scheduled checks, available event/change signals, and change-triggered re-verification. Make coverage gaps and stale evidence visible; do not silently retain verified status when current evidence is uncertain. Preserve observations and follow-up actions. Risk mapping, intervals, signal availability, freshness thresholds, gap alerts, and outage behavior remain open.


- **Safeguard monitoring unavailable — selected C:** Show an explicit monitoring-degraded state and apply risk-based restrictions/holds to affected consequential actions. Use alternative verification only when policy-authorized. After restoration, reconcile evidence and revalidate current safeguard status. Preserve outage and evidence-gap history. State labels, restriction mapping, approved alternatives, outage thresholds, reconciliation, and alerts remain open.


- **Evidence gaps after monitoring restoration — selected C:** Reconcile available authoritative evidence and explicitly document outage interval, missing evidence, provenance, and uncertainty. Perform risk-based revalidation; restoration alone does not restore verified status. Maintain applicable holds/restrictions if a material gap remains unresolved. Gap materiality, sources, reconstruction limits, revalidation mapping, and release criteria remain open.

### Decision 62 — Evidence-gap materiality (working draft)
- Materiality is determined by policy-defined criteria considering risk, missing interval/extent, evidence criticality, and plausible impact.
- AI may summarize evidence and explain implications, but may not invent or override criteria.
- If criteria are absent, ambiguous, or inapplicable, hold/restrict affected consequential work pending authorized review.
- Record the evidence considered, provenance, uncertainty, criteria/version applied, assessment, and resulting status.
- Acceptance tests: CN-296 policy criteria drive assessment; CN-297 AI explanation does not override policy; CN-298 ambiguous/uncovered case remains held; CN-299 provenance and uncertainty are recorded; CN-300 restoration alone does not reinstate verified status.

### Decision 63 — Ownership and versioning (working draft)
- A designated authorized policy owner approves and versions evidence-gap materiality criteria.
- Each change records rationale, effective date, version, and assessed impact; prior versions and change history remain traceable.
- Assessments use the currently applicable approved version; AI cannot invent missing rules.
- Ambiguous ownership/applicability/criteria keeps affected consequential work held or restricted pending authorized review.
- Acceptance tests: CN-301 owner authorization is required; CN-302 version and effective date are captured; CN-303 rationale and impact are recorded; CN-304 prior versions remain traceable; CN-305 ambiguous applicability causes a hold/review.

### Decision 64 — Impact-based reassessment (working draft)
- A criteria change triggers an impact assessment to identify existing assessments potentially affected by the new version.
- Reassess affected assessments against the currently applicable approved criteria version.
- Preserve prior assessment results, criteria versions, and change history; do not silently replace historical records.
- Apply proportionate holds/restrictions to affected consequential work while material impact is unresolved or required reassessment is incomplete.
- Avoid blanket reassessment of unrelated items unless an impact basis is identified.
- Acceptance tests: CN-306 change triggers impact assessment; CN-307 affected assessments use current applicable version; CN-308 historical results/version are retained; CN-309 affected consequential work is restricted when reassessment is pending; CN-310 unrelated assessments are not automatically invalidated without impact basis.

### Decision 65 — Reassessment-pending status (working draft)
- Preserve the prior verified result and its criteria version as historical evidence.
- Display `REASSESSMENT PENDING` or an equivalent explicit uncertainty state as the current status until required reassessment completes.
- Apply proportionate restrictions to affected consequential work while pending.
- After reassessment, update current status using the applicable approved criteria version and available evidence.
- A criteria change alone does not automatically make every affected assessment INVALID, and historical verification must not be presented as current verification.
- Acceptance tests: CN-311 historical result/version retained; CN-312 pending status displayed; CN-313 affected consequential work receives proportionate restrictions; CN-314 status changes only after reassessment evidence is evaluated; CN-315 no blanket INVALID assignment solely from criteria change.

### Decision 66 — Risk-based deadline and escalation (working draft)
- Reassessment review deadlines are defined by approved policy and risk level.
- A missed deadline preserves pending status and applicable restrictions and triggers authorized review/escalation.
- A deadline breach never implies approval, resolution, accepted uncertainty, or automatic restoration of prior status.
- Record due date/deadline rule, overdue state, escalation attempts and outcomes, restrictions, and final disposition.
- Acceptance tests: CN-316 deadline follows approved risk-based policy; CN-317 overdue assessment stays pending; CN-318 restrictions persist; CN-319 authorized escalation is triggered and recorded; CN-320 deadline expiry never automatically restores prior status.

### Decision 67 — Controlled unresolved state (working draft)
- Exhaustion of policy-defined escalation levels/attempts without response produces a persistent unresolved state.
- Applicable restrictions remain active; only explicitly permitted monitoring and demonstrably safe, independent work may continue.
- Further consequential action requires an authorized decision and required verification; silence is not approval or resolution.
- Record escalation levels/attempts, known delivery/response status, unresolved disposition, restrictions, and subsequent decisions.
- Acceptance tests: CN-321 escalation exhaustion sets unresolved state; CN-322 restrictions persist; CN-323 only permitted monitoring/safe independent work continues; CN-324 no consequential action without authorized decision and verification; CN-325 silence never counts as approval/resolution.

### Decision 68 — Risk-based review and change triggers (working draft)
- Persistently unresolved issues receive periodic review on policy-defined, risk-based schedules.
- Relevant new evidence, policy changes, authority availability, or issue-state changes trigger earlier review.
- Record the trigger, evidence/state considered, outcome, and remaining uncertainty.
- Review initiation or completion alone does not lift restrictions or authorize consequential work; required release conditions and authorized decisions still apply.
- Acceptance tests: CN-326 risk-based periodic review is scheduled; CN-327 relevant change triggers early review; CN-328 trigger and evidence are recorded; CN-329 unresolved uncertainty is preserved; CN-330 review alone does not lift restrictions.

### Decision 69 — Explicit unresolved disposition (working draft)
- When a review cannot resolve an issue, document remaining evidence gaps, attempted checks, and open questions.
- Preserve applicable holds and restrictions.
- Set the next review or escalation according to policy.
- Do not automatically close after repeated unsuccessful reviews; closure requires an authorized decision plus required verification.
- Acceptance tests: CN-331 unresolved disposition records evidence gaps; CN-332 attempted checks and open questions are recorded; CN-333 applicable restrictions remain; CN-334 next review/escalation follows policy; CN-335 repeated unsuccessful reviews do not auto-close the issue.

### Decision 70 — Review scheduling for unresolved issues (working draft)
- Set default next-review deadlines according to policy-defined risk levels.
- Permit an authorized reviewer to approve an earlier or later date only where policy allows, recording the reason.
- Track due and overdue review status explicitly.
- Overdue status is not resolution or authorization; applicable holds/restrictions persist.
- Acceptance tests: CN-336 risk-based policy deadline is applied; CN-337 authorized schedule override is policy-gated and reason-recorded; CN-338 overdue reviews are surfaced; CN-339 overdue status does not close the issue or lift restrictions.

### Decision 71 — Overdue review handling (working draft)
- Persist and visibly surface overdue review status.
- Trigger risk-based escalation according to approved policy.
- If escalation cannot proceed or no authorized recipient is available, keep applicable holds/restrictions in force.
- Do not treat a missed deadline as approval, resolution, or hold-release authority.
- Acceptance tests: CN-340 overdue status is recorded and surfaced; CN-341 escalation follows policy-defined risk; CN-342 unavailable escalation leaves restrictions active; CN-343 deadline miss cannot auto-approve, resolve, or release a hold.

### Decision 72 — Reviewer response after escalation (working draft)
- Validate the reviewer's authority and the scope of the decision before acting on it.
- Record the response, rationale, and remaining uncertainty.
- Complete required evidence checks and verification before updating issue status or restrictions.
- Do not treat receipt of a response alone as resolution or permission to release a hold.
- Acceptance tests: CN-344 reviewer authority and decision scope are validated; CN-345 response, rationale, and uncertainty are recorded; CN-346 required evidence/verification gates status changes; CN-347 response receipt alone cannot resolve an issue or release a hold.

### Decision 73 — Ambiguous or incomplete reviewer response (working draft)
- Set status to `AMBIGUOUS / INCOMPLETE` when the reviewer response is unclear or incomplete.
- Record the missing details or conflicts and request clarification from an authorized reviewer.
- Keep applicable holds and restrictions active while ambiguity remains.
- AI must not infer missing authorization, intent, or approval.
- Acceptance tests: CN-348 ambiguous/incomplete response is explicitly marked; CN-349 missing/conflicting details are recorded; CN-350 clarification is routed to an authorized reviewer; CN-351 restrictions persist and no missing authorization is inferred.

### Decision 74 — Clarification follow-up without response (working draft)
- Limit follow-up attempts according to approved policy and record each attempt and delivery/status outcome.
- After the permitted limit, use an authorized escalation route.
- If no response is received, preserve the ambiguous/incomplete state and applicable holds/restrictions.
- Silence or non-response must never be treated as approval, resolution, or hold-release authority.
- Acceptance tests: CN-352 follow-up attempts respect policy limits; CN-353 attempt and delivery/status history is recorded; CN-354 exhausted follow-ups trigger authorized escalation; CN-355 non-response preserves ambiguity and restrictions and cannot count as approval.

### Decision 75 — Exhausted escalation without clarification (working draft)
- After policy-defined escalation routes and attempts are exhausted without clarification, set `ESCALATION EXHAUSTED — UNRESOLVED`.
- Record escalation attempts and the absence of a response.
- Maintain applicable holds and restrictions and set the next review according to policy.
- No automatic approval, closure, or hold release may result from exhausted escalation.
- Acceptance tests: CN-356 exhausted routes/attempts set the explicit status; CN-357 escalation history and missing response are recorded; CN-358 restrictions persist and next review is policy-based; CN-359 exhaustion cannot auto-approve, close, or release a hold.

### Decision 76 — Ownership for exhausted escalation (working draft)
- Retain the existing authorized owner in the issue record.
- When the owner is unavailable or transfer is necessary, allow formal reassignment only to an alternate authorized by policy.
- If no valid alternate exists, set `OWNER UNAVAILABLE`; keep applicable restrictions and review status intact.
- AI must not independently reassign ownership or expand authority.
- Acceptance tests: CN-360 existing owner remains traceable; CN-361 reassignment requires a policy-authorized alternate and formal assignment; CN-362 absence of a valid alternate sets `OWNER UNAVAILABLE`; CN-363 restrictions persist and AI cannot transfer authority.

### Decision 77 — `OWNER UNAVAILABLE` handling (working draft)
- Follow policy-defined timeframes and authorized escalation paths.
- Permit assignment only to a pre-authorized alternate or through a formally authorized leadership decision.
- If no valid assignment exists, retain `OWNER UNAVAILABLE`, maintain applicable restrictions, and set the next review per policy.
- Notification, non-response, or silence does not transfer authority.
- Acceptance tests: CN-364 timing and escalation follow policy; CN-365 assignment requires a valid authorization path; CN-366 no valid assignment preserves unavailable status and restrictions; CN-367 notification/silence never transfers authority.

### Decision 78 — Undefined escalation timeframe (working draft)
- Treat a missing policy-defined escalation timeframe as a policy gap; do not substitute an unapproved default or AI-estimated deadline.
- Request clarification or a policy update from a policy-authorized owner.
- Until resolved, record `ESCALATION TIMING UNDEFINED`, maintain applicable restrictions, and allow only explicitly permitted safe monitoring.
- AI must not create a deadline or authority independently.
- Acceptance tests: CN-368 detects a missing timeframe; CN-369 records the undefined-timing state and requests authorized clarification; CN-370 maintains restrictions and limits activity to permitted monitoring; CN-371 confirms no default or AI-invented deadline is applied.

### Decision 79 — Undefined review timing (working draft)
- Review timing must come from applicable policy or formally authorized reviewer direction, where policy permits.
- If no valid schedule is defined, record `REVIEW TIMING UNDEFINED` and request clarification.
- Maintain applicable restrictions and permit only explicitly authorized safe monitoring while unresolved.
- AI must not create a review deadline independently.
- Acceptance tests: CN-372 uses only a valid policy/authorized direction; CN-373 records undefined timing and requests clarification; CN-374 preserves restrictions and limits activity to permitted monitoring; CN-375 rejects AI-invented review deadlines.

### Decision 80 — No response to policy clarification request (working draft)
- Use only follow-ups, deadlines, and escalation routes defined by applicable policy.
- If those mechanisms are undefined, record the policy gap and request direction from an authorized policy owner.
- Preserve the undefined status and applicable restrictions until valid direction is received.
- AI must not generate or apply a fallback policy.
- Acceptance tests: CN-376 uses only policy-defined follow-ups/escalation; CN-377 records a gap when those mechanisms are absent; CN-378 preserves status and restrictions pending valid direction; CN-379 rejects AI-generated fallback policy.

### Decision 81 — Policy governance unavailable (working draft)
- Use only an alternate governance contact or decision path already defined and authorized by policy.
- If no valid path exists, record `POLICY GOVERNANCE UNAVAILABLE`.
- Maintain applicable restrictions and allow only explicitly permitted safe monitoring while unresolved.
- AI must not appoint alternate authority or independently resolve the policy gap.
- Acceptance tests: CN-380 accepts only policy-defined authorized alternates; CN-381 records governance-unavailable status when no path exists; CN-382 preserves restrictions and limits activity to permitted monitoring; CN-383 prevents AI-created authority or policy-gap resolution.

### Decision 82 — Governance-unavailable review triggers (working draft)
- Use only policy-defined review triggers and timing.
- If triggers or timing are undefined, record `GOVERNANCE REVIEW TRIGGER UNDEFINED` and request authorized governance clarification.
- Relevant new information may be recorded but must not automatically change authority or restrictions.
- AI must not independently create review schedules or governance authority.
- Acceptance tests: CN-384 uses only policy-defined triggers/timing; CN-385 surfaces the undefined-trigger state and requests authorized clarification; CN-386 records new information without automatic authority/restriction changes; CN-387 prevents AI-created review schedules or authority.

### Decision 83 — Critical information during a governance gap (working draft)
- Preserve the new information and its evidence/provenance.
- Assess urgency only against available approved criteria; apply only policy-authorized protective measures.
- If criteria or authority are unclear, record `CRITICAL INFORMATION — GOVERNANCE REVIEW REQUIRED`.
- Maintain applicable restrictions and permit only explicitly authorized safe containment or monitoring.
- AI must not independently expand its authority.
- Acceptance tests: CN-388 preserves information and evidence; CN-389 uses approved urgency criteria only; CN-390 records the required status when authority/criteria are unclear; CN-391 maintains restrictions and blocks unauthorized measures or AI authority expansion.

### Decision 84 — Critical notification during a governance gap (working draft)
- Use only policy-authorized recipients and approved notification channels.
- If the recipient or channel is undefined or unavailable, record `CRITICAL NOTIFICATION PATH UNAVAILABLE`.
- Track notification delivery status accurately and maintain applicable restrictions.
- AI must not invent recipients, channels, or escalation authority.
- Acceptance tests: CN-392 validates recipient authorization; CN-393 validates approved channel use; CN-394 records unavailable notification paths and delivery state; CN-395 preserves restrictions and prevents AI-invented recipients/channels/authority.

### Decision 85 — Failed critical notification delivery (working draft)
- Apply only policy-authorized retry limits and explicitly approved fallback channels.
- Record notification delivery failure or uncertainty accurately.
- If no authorized route is available, set `CRITICAL NOTIFICATION DELIVERY UNRESOLVED`.
- Maintain applicable restrictions and permit only explicitly authorized safe monitoring or containment.
- AI must not independently switch channels or invent escalation authority.
- Acceptance tests: CN-396 enforces policy retry limits; CN-397 permits only approved fallback channels; CN-398 records unresolved delivery state when no route exists; CN-399 maintains restrictions and prevents unauthorized channel switching/authority invention.

### Decision 86 — No acknowledgement after delivery (working draft)
- Represent delivery and acknowledgement as separate statuses.
- Use only policy-defined acknowledgement deadlines, reminder limits, and escalation routes.
- Apply policy-defined escalation/status updates when a deadline is missed.
- Lack of acknowledgement must not be interpreted as resolution or approval.
- If rules are undefined, record the policy gap, maintain applicable restrictions, and prohibit AI-invented deadlines or escalation authority.
- Acceptance tests: CN-400 distinguishes delivery from acknowledgement; CN-401 enforces policy-defined deadlines/reminder limits/routes; CN-402 treats missed deadlines per policy; CN-403 confirms silence is not approval/resolution and no AI-invented timing/authority is applied.

### Decision 87 — Acknowledgement without issue resolution (working draft)
- Store acknowledgement as a communication event separate from issue state.
- Keep issue status independently tracked.
- Acknowledgement must not close an issue, release restrictions, grant approval, or transfer ownership.
- Further follow-up must follow applicable policy and authorized workflow.
- Acceptance tests: CN-404 records acknowledgement separately; CN-405 keeps issue unresolved after acknowledgement; CN-406 prevents acknowledgement from releasing restrictions/granting approval/transferring ownership; CN-407 limits follow-up to authorized policy/workflow.

### Decision 88 — Closure verification fails or is unavailable (working draft)
- Use policy-required verification before closure.
- Successful verification permits the authorized closure process to proceed.
- Failed, conflicting, or unavailable verification sets `CLOSURE UNVERIFIED`.
- Record the evidence gap and maintain applicable restrictions.
- Permit only policy-authorized review or recovery steps; AI must not invent verification or closure criteria.
- Acceptance tests: CN-408 requires policy-defined verification; CN-409 sets `CLOSURE UNVERIFIED` on failed/conflicting/unavailable evidence; CN-410 records evidence gaps and maintains restrictions; CN-411 blocks invented criteria and unauthorized recovery.

### Decision 89 — Contradictory evidence after closure verification (working draft)
- Preserve contradictory evidence, provenance/source, and timestamp.
- Assess evidence relevance and reliability using applicable policy.
- While impact is unclear, set `CLOSURE UNDER REVIEW` and maintain necessary restrictions.
- Reopening, retaining closure, or further investigation requires the authorized policy-defined process.
- AI must not independently reverse or confirm closure.
- Acceptance tests: CN-412 preserves contradictory evidence/source/timestamp; CN-413 uses applicable policy for relevance/reliability; CN-414 sets review status and maintains restrictions while impact is unclear; CN-415 blocks AI-only closure reversal/confirmation.

### Decision 90 — Closure after review of contradictory evidence (working draft)
- Preserve evidence that resolves the contradiction and the reviewer disposition.
- Require applicable policy-defined closure criteria and verification.
- Complete independent verification wherever policy requires it.
- If any criterion is missing, conflicting, or unverifiable, retain `CLOSURE UNVERIFIED` and applicable restrictions.
- Reviewer confirmation alone cannot substitute for missing evidence.
- Acceptance tests: CN-416 preserves contradiction-resolution evidence and reviewer disposition; CN-417 enforces applicable closure criteria; CN-418 requires independent verification when policy says so; CN-419 retains unverified status/restrictions for missing, conflicting, or unverifiable criteria.

### Decision 91 — Incomplete or unavailable audit record after closure (working draft)
- Explicitly record missing or unavailable audit evidence; preserve available records and provenance.
- Set `CLOSURE AUDIT REVIEW REQUIRED`.
- Assess whether closure remains valid or further review is needed under applicable policy.
- Maintain necessary restrictions during review.
- AI must not fabricate audit entries or independently reopen or re-close the issue.
- Acceptance tests: CN-420 records audit gaps explicitly; CN-421 preserves available records/provenance; CN-422 sets review status and applies policy-based assessment; CN-423 maintains restrictions and blocks fabricated audit entries or AI-only reopening/re-closure.

### Decision 92 — Audit record repaired but completeness unverified (working draft)
- Record repaired entries, their source, repair method, and confidence/verification status.
- Distinguish original evidence from reconstructed information.
- If completeness cannot be verified, set `AUDIT COMPLETENESS UNVERIFIED` and maintain applicable restrictions.
- Update review status only after policy-defined criteria and authorized review.
- AI must not invent missing history.
- Acceptance tests: CN-424 records repair provenance/method/status; CN-425 distinguishes original from reconstructed evidence; CN-426 preserves unverified status and restrictions when completeness is unknown; CN-427 blocks invented history and unauthorized status changes.

### Decision 93 — Clearing audit review after completeness verification (working draft)
- Preserve evidence and provenance for audit-completeness verification.
- Clear audit-review status only after applicable policy criteria are satisfied and an authorized reviewer approves clearance.
- Validate issue closure status separately; audit completeness alone does not validate closure.
- If criteria are missing or uncertainty remains, maintain review status and applicable restrictions.
- Acceptance tests: CN-428 preserves completeness evidence/provenance; CN-429 blocks clearance without policy criteria and authorized approval; CN-430 validates closure separately; CN-431 retains review status/restrictions when criteria are missing or uncertainty remains.

### Decision 94 — Audit review cleared but closure validation fails (working draft)
- Track audit-review clearance and issue-closure validity as independent states.
- If closure validation fails, record `ISSUE CLOSURE VALIDATION FAILED`; if unavailable, record `ISSUE CLOSURE VALIDATION UNAVAILABLE`.
- Preserve evidence and maintain applicable restrictions.
- Reopening, retaining closure, or further review must follow the authorized policy-defined process.
- Clearing audit review must not automatically close or reopen the issue.
- Acceptance tests: CN-432 keeps audit and closure states separate; CN-433 records the correct failure/unavailable status; CN-434 preserves evidence and restrictions; CN-435 blocks automatic closure/reopening and requires authorized policy-defined handling.

### Decision 95 — Reviewer unavailable after closure validation fails (working draft)
- Record closure-validation failure or unavailability and retain an unresolved review state.
- Use only a policy-designated alternate reviewer or explicitly authorized governance route.
- If no valid route is available, set `CLOSURE REVIEWER UNAVAILABLE`.
- Maintain applicable restrictions and permit only safe monitoring/containment explicitly allowed by policy.
- AI must not independently appoint a reviewer, reverse closure, or transfer authority.
- Acceptance tests: CN-436 records validation failure/unavailability; CN-437 restricts alternate review to policy-designated/authorized routes; CN-438 sets unavailable status and maintains restrictions when no route exists; CN-439 blocks AI-only reviewer appointment, closure reversal, and authority transfer.

### Decision 96 — Resume review when authorized reviewer returns (working draft)
- Revalidate reviewer identity, authority, and review scope before resuming.
- Recheck latest issue status, closure-validation evidence, audit trail, applicable policy, and intervening changes.
- Reuse prior work only where evidence remains verified; stale, missing, or conflicting evidence stays unresolved.
- Resume only within validated scope; maintain status/restrictions while required checks remain incomplete.
- Acceptance tests: CN-440 validates reviewer identity/authority/scope; CN-441 checks current issue, evidence, audit, policy, and changes; CN-442 rejects stale/missing/conflicting evidence as resolved; CN-443 prevents resumption beyond validated scope and preserves restrictions when checks are incomplete.

### Decision 97 — Missing or conflicting evidence when review resumes (working draft)
- Identify and record missing/conflicting evidence; preserve provenance and timestamps.
- Verify within bounded limits using policy-approved authoritative sources only.
- Keep affected closure determination unresolved and maintain applicable restrictions.
- Continue unaffected review work only if demonstrably safe and independent of the gap.
- Route unresolved gaps through authorized policy-defined review/escalation; AI must not independently select evidence to settle conflicts.
- Acceptance tests: CN-444 records gaps/conflicts and provenance; CN-445 restricts verification to approved sources and bounds; CN-446 preserves unresolved status/restrictions; CN-447 allows only demonstrably safe independent work; CN-448 routes unresolved gaps through authorized process and blocks AI-only evidence selection.

### Decision 98 — Evidence conflict remains unresolved after authorized review (working draft)
- Record disposition `EVIDENCE CONFLICT UNRESOLVED`.
- Preserve conflicting evidence, provenance, attempted checks, and remaining uncertainty.
- Maintain applicable restrictions.
- Consider residual-risk acceptance or bounded safe continuation only when policy permits and required authority explicitly approves; otherwise hold affected consequential work.
- Future review/change triggers must be policy-defined; review completion alone does not close the issue or release a hold.
- Acceptance tests: CN-449 records unresolved disposition and evidence history; CN-450 maintains restrictions; CN-451 requires policy permission and explicit authority for residual-risk acceptance/safe continuation; CN-452 holds affected consequential work when no permitted authorization exists; CN-453 prevents review completion alone from closing the issue or releasing a hold.

### Decision 99 — Residual-risk acceptance expires or becomes invalid (working draft)
- Expired/invalid residual-risk acceptance is not current authorization.
- Obtain fresh assessment/acceptance from an authorized reviewer as required by applicable policy.
- Keep affected consequential work held/restricted; safe independent work may continue only where policy explicitly permits.
- Preserve prior acceptance, expiry/invalidity reason, current evidence, and reassessment history.
- Expiry alone does not close the issue or accept residual risk.
- Acceptance tests: CN-454 rejects expired/invalid acceptance as current authorization; CN-455 requires policy-governed fresh assessment/acceptance; CN-456 maintains affected-work restrictions; CN-457 permits safe independent work only under explicit policy permission; CN-458 preserves acceptance/evidence history and prevents expiry-driven closure or risk acceptance.

### Decision 100 — Restrictions after fresh residual-risk acceptance (working draft)
- Record and validate fresh residual-risk acceptance.
- Revalidate current evidence, authority, safeguards, and policy-required checks before release.
- Lift only restrictions with passed release prerequisites and policy permission.
- Keep affected restrictions when evidence/checks are missing, conflicting, or uncertain.
- Acceptance approval alone is not proof that restrictions may be lifted.
- Acceptance tests: CN-459 validates fresh acceptance; CN-460 rechecks current evidence/authority/safeguards/policy; CN-461 lifts only eligible restrictions; CN-462 maintains restrictions for missing/conflicting/uncertain checks; CN-463 prevents approval-only release.

### Decision 101 — Resume work after restrictions are lifted (working draft)
- Revalidate current state, action status, authority, approval validity, applicable policy, and dependencies before resumption.
- Resume only verified, still-authorized work within the released scope.
- Material changes require fresh approval; unknown state remains paused.
- Verify and record every resumed action's outcome.
- Acceptance tests: CN-464 revalidates current state/status/authority/approval/policy/dependencies; CN-465 limits resumption to verified authorized released scope; CN-466 requires fresh approval for material changes and pauses unknown state; CN-467 verifies and records resumed-action outcomes.

### Decision 102 — Resumed action fails or outcome is unknown (working draft)
- Use approved, bounded read-only checks to reconcile actual state before deciding next steps.
- Never infer success/failure from an unknown outcome; no blind retry, rollback, or repair.
- Pause the affected action and dependent work; safe independent work may continue only when demonstrably safe.
- Retry/recovery requires explicit policy authorization and verified prerequisites.
- Acceptance tests: CN-468 requires bounded approved read-only reconciliation; CN-469 preserves unknown as unknown; CN-470 blocks blind retry/rollback/repair; CN-471 pauses affected/dependent work while permitting only demonstrably safe independent work; CN-472 requires explicit authorization and verified prerequisites for retry/recovery.

### Decision 103 — Confirmed action failure after reconciliation (working draft)
- Record confirmed failure and assess the affected workflow.
- Retry only when policy explicitly permits it, duplicate-side-effect risk is assessed, current state/prerequisites are verified, and required authorization is valid.
- Otherwise pause and present recovery options/evidence to the user or authorized reviewer.
- Failure confirmation alone does not authorize retry.
- Acceptance tests: CN-473 records confirmed failure and assesses scope; CN-474 blocks retry without explicit policy permission; CN-475 checks duplicate-side-effect risk and current prerequisites; CN-476 requires valid authorization; CN-477 pauses and presents recovery evidence/options when retry conditions are unmet.

### Decision 104 — Retry limits for eligible failed actions (working draft)
- Approved action-specific policy defines retry count, timing, and backoff.
- In the MVP, missing configured limits, unclear retry safety, or uncertain outcome prohibit automatic retry; pause and ask.
- Record every attempt in the audit trail.
- When the limit is exhausted, follow the authorized recovery path.
- Acceptance tests: CN-478 requires action-specific approved retry settings; CN-479 blocks automatic retry when limits are missing; CN-480 blocks retry when safety is unclear or outcome uncertain; CN-481 audits every attempt; CN-482 routes exhausted retries to authorized recovery.

### Decision 105 — First MVP implementation target (working draft)
**First end-to-end action:** Context Note Update.
- Prepare proposed content, reason, target note, and expected version.
- Validate identity/role authority, applicable AUTO/ASK/BLOCK policy, risk, and approval binding.
- Persist required audit/evidence before dispatch; if persistence fails, do not execute.
- Write conditionally against the expected version; preserve the previous version and reject version conflicts.
- Read back from an authoritative source and verify content and version before updating context/timeline.
- On timeout or unknown outcome, reconcile through bounded approved read-only checks; no blind retry, rollback, or repair.
- Keep the ₹0-to-₹500 note example illustrative only; it is not authorization to alter a real note.
- Acceptance tests: CN-483 validates authority/policy/risk/approval; CN-484 blocks execution if required audit persistence fails; CN-485 rejects stale expected versions; CN-486 verifies read-back content/version before context update; CN-487 leaves unknown outcomes unresolved without blind retry/rollback/repair.
