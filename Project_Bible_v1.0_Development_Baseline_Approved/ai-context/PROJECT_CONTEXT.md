# PROJECT CONTEXT — READ THIS FIRST

## PROJECT STATUS
This repository currently contains a working draft based on discussed product decisions. The user has confirmed the Review 9.7 and 9.8 recaps as accurate and confirmed Review 9.9 boundary decisions, but has not final-locked the project scope. Treat working decisions as provisional until explicit final approval.

## PRODUCT SUMMARY
Developer/builder-first Personal AI that remembers project decisions and why they were made, monitors selected important assumptions, detects relevant changes, explains impact, researches alternatives, asks permission where required, verifies approved actions, and updates project context.

Core differentiator:
“Most AI remembers what you decided. Ours remembers why—and knows when that reason stops being true.”

## WORKING MVP SCOPE
1. Decision Memory
2. Decision Health Engine
3. Assumption Watch
4. Impact & Recovery Intelligence
5. Controlled Action
6. Decision Timeline

The current product documents are root-level files: `05_requirements.md`, `07_feature_specs.md`, and `12_decisions.md`. These describe working decisions, not final-locked. Supporting team/workflow documents remain under `docs/`. Do not move files unless explicitly approved.

## SCOPE CONTROL
- New ideas go to Future Development; do not add them to the current MVP.
- For a genuine technical blocker, make only the minimum necessary change.
- Feature replacement requires review and explicit user approval.
- Record the reason, approval, and affected features for approved scope changes.
- Current out-of-scope items: email/calendar assistant, giant life-memory system, multiple AI personalities, voice assistant, mobile app, dozens of integrations, custom model training, huge multi-agent system.
- Do not invent product requirements, models, frameworks, APIs, database schema, or permission boundaries.

## PROPOSED TECHNICAL ROLES — NOT FINALIZED
NVIDIA Nemotron for reasoning; Nebius Token Factory for inference/runtime; Tavily for finding/verifying changing external information; OpenShell/NemoClaw for secure agent actions if suitable. Exact models, versions, and implementation are undecided.

## SOURCE OF TRUTH
Use this priority:
1. Explicitly human-approved final decisions, if any, recorded in `12_decisions.md`
2. Requirements in `05_requirements.md`
3. Architecture in `08_architecture.md`
4. API contract in `10_api_contract.md`
5. Database contract in `09_database.md`
6. Feature specifications in `07_feature_specs.md` and user flows in `06_user_flows.md`
7. Supporting workflow, team, and integration documents under `docs/`
8. Existing code

Working-draft or undecided entries are not final approval. If sources conflict or a requirement is missing, stop and ask instead of guessing. Do not silently choose a side.

## Approval & Action Safety
The consolidated working draft is `docs/17_approval_action_safety.md`. Treat it as provisional, preserve specific early-review exceptions, and do not treat Reviews 1008–1012 as approved; those selections remain unanswered. Do not final-lock this policy without explicit human approval.
