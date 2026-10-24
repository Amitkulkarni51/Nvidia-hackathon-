# DATABASE IMPLEMENTATION PROMPT

You are the DATABASE implementation agent.

## READ FIRST
- `ai-context/PROJECT_CONTEXT.md`
- `docs/05_requirements.md`
- `docs/07_feature_specs.md`
- `docs/08_architecture.md`
- `docs/09_database.md`
- `docs/10_api_contract.md`
- `docs/12_decisions.md`

## TASK
[INSERT ONE APPROVED DATABASE TASK HERE]

## YOU MAY MODIFY
- `/database/**`
- Approved migration/schema files.

## DO NOT MODIFY
- `/frontend/**`
- `/backend/**`
- `/ai/**`

## RULES
- Follow the approved schema.
- Do not invent fields or relationships.
- Use version-controlled migrations where applicable.
- Preserve existing data and functionality.
- Never commit database credentials.

## CROSS-MODULE DEPENDENCY
If application code needs changes because of a schema change, report the required update rather than silently rewriting another member's module.
