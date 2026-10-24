# Project Bible v1.0 — Development Baseline Approval

**Status: APPROVED AS DEVELOPMENT BASELINE**  
**Decision range:** 1–105  
**Approval scope:** Product direction, documented MVP scope, and current decision set are approved as the baseline for development.  
**Not implied:** This is not a production-readiness certification, does not mean every individual decision is final-locked, and does not authorize real-world actions or bypass safety controls.

## Product direction
“Most AI remembers what you decided. Ours remembers why—and knows when that reason stops being true.”

## MVP scope
1. Decision Memory
2. Assumption Watch
3. Decision Health (`HEALTHY / AT RISK / INVALID`)
4. Impact & Recovery
5. Controlled Actions (`AUTO / ASK / BLOCK`)
6. Decision Timeline

## First implementation target
Context Note Update using Python + FastAPI. Implement the action contract, authorization/policy gates, version-checked writes, durable audit gate, read-back verification, and conservative handling of unknown outcomes. The current code skeleton is a local development prototype only.

## Unresolved blockers remain open
- Trusted identity and role authorization.
- Real policy/risk engine and action-specific risk mapping.
- Approval binding to exact action/version/scope, including expiry and revocation.
- Authoritative verification sources per action.
- Production-grade durable/protected audit persistence.
- Crash-safe unknown-outcome reconciliation.
- Tenant/project isolation, operational limits, monitoring, security and concurrency tests.
- Other gaps listed in `docs/19_implementation_readiness_review.md` and `docs/18_policy_matrix_and_consistency_check.md`.

## Change control
- Keep implementation within the approved MVP scope.
- Document material changes, rationale, approval, and affected components.
- New ideas outside MVP go to Future Development unless needed to resolve a genuine blocker.
- Do not infer missing authority or policy. Where required rules are undefined or evidence is uncertain, preserve the documented pause/hold behavior.
- Individual decisions retain their recorded status; baseline approval does not silently convert all working drafts into final-locked decisions.

## Verification record
This archive was generated from the Decisions 5–105 Project Bible archive. ZIP integrity and required approval markers were checked after writing.
