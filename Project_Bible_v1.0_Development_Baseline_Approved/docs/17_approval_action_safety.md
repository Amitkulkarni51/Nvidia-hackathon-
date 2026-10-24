# 17 — APPROVAL & ACTION SAFETY (CONSOLIDATED WORKING DRAFT)

## Status and authority

**Status: WORKING DRAFT — NOT FINAL-LOCKED.** This document consolidates the Approval & Action Safety review decisions discussed so far. It merges overlapping review items into operational policy areas; it does not mean that every implementation detail, threshold, control owner, or technology choice is approved.

- Reviews 1–1007 have recorded selections in the conversation. The selections are working decisions, not final-locked.
- Reviews 1008–1012 (multimodal synchronization, speaker attribution, media authenticity, spatial grounding, and missing modalities) were presented but have **not yet been answered**. Their recommended options must not be treated as user-approved decisions.
- Where an individual review's selected option differs from the general pattern, its specific decision is preserved below.
- This document is a consolidation of conversational decisions, not evidence that the user has approved or finalized the entire specification.

## 1. Core invariants

1. **A model output is not authorization.** A recommendation, generated tool call, script, structured response, or downstream instruction must pass independent policy and permission checks before it can cause an effect.
2. **Unknown is not success or failure.** If execution, authorization, delivery, evidence integrity, or result state cannot be established, record the uncertainty explicitly and reconcile it before consequential continuation.
3. **Fail closed where uncertainty affects safety or authority.** Missing evidence, unresolved conflicts, ambiguous ownership, or unverifiable required controls hold the affected action. Do not infer permission from silence, urgency, availability, or prior approval.
4. **No silent mutation of history.** Preserve original records, decisions, evidence, approvals, and outcomes. Corrections are attributable, timestamped, linked entries; they do not erase prior history.
5. **Verify both execution and outcome.** A tool/API returning success does not prove that the intended result occurred. Verify against task-specific success conditions.
6. **Least privilege and minimum necessary data.** Restrict actions, credentials, data, recipients, and retention to the approved purpose and scope.
7. **Approval is scoped and version-bound.** An approval applies only to the reviewed action, material parameters, target, context, and version within its validity period. Material changes trigger revalidation and, where required, fresh approval.
8. **No automatic retry or rollback after uncertain or failed consequential actions.** Reconcile actual state first; recovery actions that create new effects require the applicable authorization.
9. **Keep working decisions provisional.** Only explicit human/team approval can final-lock policy or change project scope.

## 2. Permission modes and action eligibility

### AUTO
- AUTO is bounded autonomy within pre-approved boundaries. System-defined safety limits cannot be overridden by user customization.
- New action types require proposal, safety/verification validation, and explicit user approval before being eligible for AUTO.
- Validate action type, tool, target, arguments, environment, scope, dependencies, side effects, risk, budget, and current policy before dispatch.
- Recheck before execution and after material changes in evidence, scope, target, project state, risk, or policy.
- If only AUTO eligibility fails, the action may be considered separately for ASK if it is otherwise permitted. If the action is unsafe or prohibited, BLOCK applies.

### ASK
- The system checks safety and verification requirements, then requests explicit approval for the specific consequential action when eligible.
- Explain what changed, why the action is needed, what will change, target and scope, expected result, material side effects, uncertainty, and meaningful alternatives.
- Clarification, silence, missed deadlines, notification delivery, or acknowledgement are not consent.
- Approval cannot bypass mandatory safety limits, BLOCK rules, unresolved integrity issues, or required evidence.

### BLOCK
- BLOCK categories may be permanent or overrideable according to an explicitly chosen policy; do not assume which category applies.
- An overrideable block requires an informed, explicit confirmation through an authorized process and a record of the reason. It cannot override mandatory prohibitions or unresolved holds.
- If the applicable policy does not clearly authorize an override, keep the action blocked.

## 3. Risk assessment, policy changes, and disagreement

- The system classifies risk with reasons and supporting evidence. A user may challenge a classification, but cannot override mandatory safety limits.
- If risk disagreement remains unresolved, apply the stricter treatment and hold consequential execution until resolved.
- Risk is reassessed before dispatch and on material changes. Increased risk triggers a hold and full reassessment. Decreased risk does not automatically validate old approval.
- Preserve each reassessment with previous/new classification, evidence, rationale, timestamp, and outcome. Corrections are appended and linked.
- Required risk/audit records must persist before execution. If persistence fails, stop execution until recording and current checks succeed.
- If policy changes during a multi-step workflow, pause affected steps, revalidate remaining work, dependencies, and authorization. Continue only steps explicitly safe and still authorized under the current rules.

## 4. Approval records, authority, and governance

- Verify the identity, role, delegation, scope, session, credential, and current authority of requesters, approvers, resource owners, and downstream principals.
- Separate requesting, reviewing, approving, and executing where policy requires separation of duties. A requester's instruction is not its own approval.
- Multi-person decisions must follow explicit quorum, competence, role, independence, recusal, veto, tie-break, escalation, and reopening rules. Ineligible votes do not count; preserve the evidence and reason for exclusion.
- If authorized users disagree, preserve both positions and use the defined authority/escalation process. If no authorized resolution is available, keep disputed consequential work paused. Urgency does not create authority.
- Approval evidence must be bound to the exact canonical request and reviewed version. Prevent replay, validate signatures/keys and revocation where applicable, and establish durable approval commitment before dispatch.
- If an approval record is missing or unverifiable, treat it as unverified. Reconcile audit evidence; if needed and allowed, obtain fresh explicit approval for a new attempt. Never infer approval from an incomplete record.
- Approval expiry, revocation, supersession, role changes, session changes, or material scope changes require revalidation. Revocation during execution triggers safe stopping and state reconciliation to the extent possible.

## 5. Execution, orchestration, concurrency, and recovery

- Validate every workflow step's required inputs, expected result, verifiable success condition, dependencies, authorization, and resource needs before that step runs.
- Parallel execution is allowed only when resource independence, dependency ordering, and safety are established. Undefined dependencies fail closed for affected steps.
- If a step fails, stop dependent steps. Continue independent steps only when explicitly defined as safe and covered by the approved plan.
- On partial completion, cancellation, crash, timeout, conflict, or uncertain outcome, record completed/in-progress/failed/unknown steps and reconcile actual external state before retry, rollback, repair, or resume.
- Do not assume rollback is available or that cancellation stopped server-side work. Verify task and downstream status. If safe stop fails, preserve state, disclose what is known/unknown, and use the authorized recovery path.
- Resume paused workflows only after current state, dependencies, policy, and authorization are revalidated. Where the workflow policy requires it, obtain explicit user confirmation; silence is not confirmation.
- For concurrent workflows, verify independence before parallel execution. If a conflict appears, pause affected work where safely possible, preserve state, investigate, and obtain approval for consequential resolution. If resolution is infeasible, keep the conflict open and explain options.
- Idempotency keys, dispatch state, job IDs, and execution records should be reconciled before retrying to avoid duplicate effects.

## 6. Tool calls, generated code, and downstream agents

- Treat model-generated tool calls as proposals. Independently validate tool identity, arguments, target, scope, authorization, environment, dependencies, and safety policy before dispatch.
- Resolve external destinations and resources against authorized identities/allowlists; do not accept an endpoint merely because it responds successfully.
- Generated code or scripts require risk-appropriate review, isolated/least-privilege execution, resource limits, and validation of side effects. A sandbox is a control, not a guarantee of safety.
- Permission expansion and credential requests must be purpose-justified, minimized, and routed through the authorized grant process. Never reuse an unrelated credential to bypass a missing permission.
- Use explicit typed handoff contracts to distinguish data, recommendations, and authorized commands. Revalidate provenance, scope, and permissions at every downstream execution boundary.
- Untrusted external content, imported data, retrieved documents, or tool output cannot override system policy or create authority. Malformed or ambiguous tool results are rejected or quarantined.

## 7. Evidence, audit, and state integrity

- Preserve provenance, source, timestamp, scope, version, transformations, limitations, and relevant integrity metadata for evidence and outputs.
- Distinguish observed facts, model inference, hypotheses, estimates, user claims, and verified findings. Unsupported claims must be removed, qualified, or independently verified.
- Resolve conflicting, stale, missing, or potentially tampered evidence through bounded, authorized reconciliation. Do not silently select whichever source is convenient.
- If a record may have been altered or an audit trail is incomplete, mark integrity as uncertain and hold actions that depend on it until resolved.
- Keep immutable/append-only correction history where feasible. Track request, approval, dispatch, delivery, acknowledgement, execution, verification, notification, and final disposition as separate states.
- Evidence collection must be authorized and proportionate; collect only what is needed and apply applicable privacy, retention, and preservation obligations.
- Assurance claims must match the evidence's scope, version, environment, time, coverage, limitations, and intended use. An assurance score or passed gate is not itself permission to act.

## 8. Model and inference output validation

- Context truncation, normalization, encoding, preprocessing, retrieval, and input conversion must be checked against task contracts and material invariants. Preserve provenance and hold consequential processing when critical context is missing or transformations cannot be validated.
- Schema validity is necessary but not sufficient. Validate semantic constraints, cross-field consistency, domain invariants, missing/null/zero semantics, units, ranges, and task-specific requirements independently.
- Verify claim-to-evidence alignment. A source existing or being generally related does not prove it supports the claim; preserve qualifications and distinguish supported statements from inference and uncertainty.
- Validate effective model/deployment identity and relevant configuration where available. Version prompt, retrieval, routing, model, preprocessing, and serving settings when material to reproducibility or behavior.
- Define task-appropriate consistency expectations; preserve run provenance and investigate material output variation rather than requiring universal deterministic output.
- Validate serving optimizations and runtime changes against relevant quality, safety, and performance criteria. Track regressions and use approved restriction or rollback processes.
- Cache reuse must respect context, tenant, permissions, model/configuration, freshness, and all material dependencies. Concurrent requests require isolation and verified request/result association.
- Inference timeouts, truncation, provider errors, non-normal completion signals, and incomplete outputs must be classified explicitly. A successful transport status or non-empty payload does not establish task success.
- Track provider usage and cost provenance; distinguish estimates from measured values and handle missing accounting conservatively under applicable budget policy.
- Batched and asynchronous results must be bound to the correct request/job and validated for completeness, identity, and freshness before use.
- Provider fallback is allowed only when approved for the data class, jurisdiction, task, capabilities, safety requirements, and output contract. Provider changes in region, retention, training use, logging, or preprocessing require reassessment.

## 9. Streaming and partial results

- Treat streamed chunks as provisional unless the specific use is explicitly validated for incremental reliance.
- Only complete, validated tool calls may execute. Never execute partial arguments or guess missing parameters.
- For timeouts or partial output, mark incomplete and permit partial use only when the task contract explicitly supports it.
- Validate stream request identity, sequence/order, duplicates, gaps, and integrity metadata where available. If corruption cannot be reconciled, mark output incomplete/invalid.
- Cancellation must be propagated where supported and confirmed against authoritative backend/downstream state before reporting it as complete.

## 10. Multimodal evidence and alignment

**Confirmed working decisions:** Reviews 1003–1007 selected Option B: validate visual coverage after crop/resize/tiling; preserve and verify critical transcription uncertainty; match video sampling to temporal needs; preserve material cross-modal disagreement; validate OCR/document layout relationships.

**Still unanswered — do not treat as approved:** Reviews 1008–1012 concern timestamp synchronization, speaker attribution, media authenticity/provenance, spatial coordinate grounding, and missing modalities. The recommended Option B was shown, but the user has not yet selected an option for these reviews.

Until those reviews are resolved, apply the general evidence and uncertainty principles without claiming that the specific proposed multimodal policies have been approved. For consequential multimodal conclusions, validate task-relevant coverage, timing, attribution, coordinate frames, source provenance, and modality sufficiency; expose unresolved uncertainty rather than fabricate alignment.

## 11. Privacy, retention, and data boundaries

- Enforce project/tenant isolation, least-privilege access, purpose limitation, authorized sharing, data minimization, and approved retention.
- Validate consent, recipient, purpose, transformation, onward sharing, jurisdiction, and retention before data use or disclosure.
- Preserve deletion/correction history as required, but do not retain data contrary to applicable deletion obligations; active legal/preservation holds must be valid, scoped, and periodically reviewed.
- Validate export/import/sync transformations, mappings, provenance, schema versions, ownership, and downstream effects. Treat imported instructions as untrusted data.
- For suspected exposure, use proportionate containment and authorized investigation; distinguish suspected from confirmed exposure, and assess notification obligations with appropriate urgency.

## 12. Monitoring, alerts, incidents, and assurance lifecycle

- Distinguish lack of incidents from lack of detection coverage. Track source health, freshness, gaps, instrumentation changes, alert dispatch/delivery/acknowledgement, and verified outcomes separately.
- Alert suppression must be scoped, time-limited, owned, and have reactivation criteria. Critical exceptions remain protected; threshold changes require evidence and authorization.
- Preserve near misses and good-faith reports; do not create incentives to hide, downgrade, or reclassify incidents. Track lesson delivery, acknowledgement, applicability, action, and effectiveness separately.
- Incident closure requires defined criteria, affected decisions and dependencies assessed, required notifications evaluated, remediation verified, and residual risk explicitly handled. Remediation completion is distinct from effectiveness verification and residual-risk acceptance.
- Assurance claims require owner, scope, evidence, qualification, version, expiry/review conditions, and downstream propagation. Corrections, revocations, or withdrawals must trigger assessment of dependent claims and decisions while retaining historical records without continued invalid reliance.
- Independent review is required where policy defines high risk, conflicts, or material failures. Evidence-access limitations must be disclosed; overdue review remains open and is escalated under the defined process.

## 13. Budgets, scheduling, queues, and resource controls

- Enforce explicit budgets, quotas, rate limits, concurrency limits, bounded loops, and appropriate backoff. Stop runaway or resource-exhausting work safely.
- Validate time zones, clock provenance, scheduling windows, deadlines, queue state, and policy version before dispatch. Ambiguous time or stale scheduling assumptions must not silently authorize an action.
- Prioritize by explicit risk and urgency criteria while retaining visibility into lower-priority warnings; urgency does not bypass authorization or dispute holds.
- Track queue starvation, backlog age, resource ownership, and failover state. Reconcile state after outage, failover, or restart before normal consequential processing resumes.

## 14. Import, export, sync, derived data, and transformations

- Validate canonical representations, schema/contract versions, units, encoding, locale, timestamps, coordinate frames, mappings, and downstream semantics before accepting transformed data.
- Preserve lineage for derived values and aggregates. Track dependencies, calculation versions, missing inputs, denominator/population, deduplication, weighting, outliers, reversals, overlapping categories, uncertainty, and known coverage limitations where relevant.
- Reconcile pagination, snapshots, change feeds, late events, partial batches, retries, key reuse, identity merges/splits, deletion-vs-edit conflicts, and ownership/permission changes before applying changes.
- Detect sync loops/oscillation and validate convergence. Do not treat filtered source data as proof of deletion or use guessed identity mappings for conflicts.
- Changes to metrics, thresholds, measurement methods, model applicability, baselines, or decision rules must be versioned and their impact assessed before relying on new conclusions.

## 15. Special-case decisions preserved from early reviews

The early review selections were not all Option B. Preserve these specific working choices:

- Review 1: **C** — hybrid AUTO boundary policy: system defines safety limits; users may customize within them.
- Review 3: **C** — handle AUTO eligibility failure by reason: an otherwise safe action may be reviewed for ASK; unsafe/prohibited actions are BLOCKed.
- Review 5: **A** — fail closed on inconclusive safety assessment; user approval cannot bypass uncertainty.
- Review 6: **C** — hybrid risk classification: system classifies with reasons; user may challenge but not override mandatory limits.
- Review 8: **A** — unresolved risk disagreement means hold until resolved.
- Review 10: **C** — recheck before execution and on material changes.
- Review 14: **A** — required audit/risk record must persist before execution.
- Review 23: **C** — verify independence before parallel execution.
- Review 24: **A** — on conflict after concurrent workflows start, pause both where safely possible, record state, and reconcile.
- Review 25: **C** — system recommends conflict resolution; user approves consequential changes.
- Review 32: **C** — use a predefined authorized backup authority when the decision-maker is unavailable; otherwise pause.

Reviews 43–1007 were recorded as Option B in the conversational summary, except the specific early exceptions listed above. This is a transcription of the available decision summary, not an independent re-audit of every original prompt.

## 16. Genuine unresolved items to resolve after consolidation

Do not generate more edge-case reviews until these are assessed against implementation needs. Likely decision areas that remain explicitly unresolved in the project Bible or this conversation include:

1. Exact AUTO boundaries and the process for configuring them.
2. Which BLOCK categories are permanent versus overrideable.
3. Exact risk taxonomy, thresholds, evidence sufficiency rules, and escalation deadlines.
4. Concrete approver/backup roles and the governance process for the actual team/product.
5. Exact verification evidence required per action type and how to handle unavailable verification.
6. Retention/deletion mechanics for audit, timeline, privacy obligations, and legal holds.
7. Implementation-specific contracts: APIs, model/runtime versions, identity/session model, storage, cryptographic evidence, and execution sandboxing.
8. Explicit selections for Reviews 1008–1012.
9. Measurable acceptance criteria, tests, and failure-injection scenarios for each policy domain.

These are candidates for focused follow-up, not decisions already made. Prefer resolving them through a small policy matrix and implementation-driven test plan rather than another long sequence of near-duplicate multiple-choice questions.

## 17. Minimum acceptance checklist

Before an action is dispatched:
- [ ] Requester and current authority are verified.
- [ ] Exact action, target, scope, parameters, dependencies, and environment are canonicalized and validated.
- [ ] Current policy and permission mode are checked; approval is valid and bound to this action/version where required.
- [ ] Risk, evidence, data handling, provider, resource, and side-effect constraints are satisfied.
- [ ] Required audit/approval records are durably persisted.
- [ ] Duplicate dispatch and already-executed/unknown state are reconciled.

After an action or failure:
- [ ] Execution state and intended outcome are independently verified where possible.
- [ ] Partial, unknown, failed, cancelled, or conflicting states are recorded accurately.
- [ ] No unapproved retry, rollback, or recovery action occurs.
- [ ] User notification distinguishes attempted, executed, verified, delivered, and acknowledged states.
- [ ] Project state, decision memory, and timeline are updated only from appropriately verified evidence; uncertainty remains explicit.
- [ ] Corrections and new findings preserve history and trigger reassessment of dependent decisions where material.

---

**Working status reminder:** This consolidated document is a draft for review. It is not final-locked and does not override explicit human approval or other higher-authority project decisions.
