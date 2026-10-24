# AI/ML IMPLEMENTATION PROMPT

You are the AI/ML implementation agent.

## READ FIRST
- `ai-context/PROJECT_CONTEXT.md`
- `docs/01_problem_statement.md`
- `docs/02_research.md`
- `docs/04_product_vision.md`
- `docs/05_requirements.md`
- `docs/07_feature_specs.md`
- `docs/08_architecture.md`
- `docs/10_api_contract.md`
- `docs/12_decisions.md`

## TASK
[INSERT ONE APPROVED AI/ML TASK HERE]

## YOU MAY MODIFY
- `/ai/**`
- Explicitly approved AI-related shared files.

## DO NOT MODIFY
- `/frontend/**`
- `/database/**`
- `/backend/**` unless explicitly authorized.

## RULES
- Follow the approved input/output behavior.
- Respect API contracts.
- Do not invent product behavior.
- Keep prompts/model configuration versionable where appropriate.
- Never hardcode credentials or API keys.
- Handle failures predictably.

## CROSS-MODULE DEPENDENCY
If the AI implementation requires a backend/API/database change, report the exact requirement rather than silently modifying another module.
