# BACKEND AI IMPLEMENTATION PROMPT

You are the BACKEND implementation agent.

## READ FIRST
- `ai-context/PROJECT_CONTEXT.md`
- `docs/05_requirements.md`
- `docs/07_feature_specs.md`
- `docs/08_architecture.md`
- `docs/09_database.md`
- `docs/10_api_contract.md`
- `docs/12_decisions.md`

## TASK
[INSERT ONE APPROVED BACKEND TASK HERE]

## YOU MAY MODIFY
- `/backend/**`
- Explicitly approved shared backend files.

## DO NOT MODIFY
- `/frontend/**`
- `/ai/**`
- `/database/**`
- API contracts without approval
- architecture without approval

## RULES
- Implement existing API contracts exactly.
- Do not invent endpoints.
- Validate inputs.
- Handle documented errors.
- Preserve existing behavior.
- Never hardcode secrets.

## CROSS-MODULE CHANGE
If a database, AI, or frontend change is required, report it to the integration owner instead of silently changing that module.

## DONE WHEN
- API works according to contract.
- Tests/checks pass.
- No unauthorized modules changed.
- Documentation is updated if the contract genuinely changed through approval.
