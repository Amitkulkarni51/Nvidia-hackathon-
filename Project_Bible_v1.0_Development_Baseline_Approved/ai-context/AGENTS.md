# AGENTS.md — SHARED AI INSTRUCTIONS

Read `PROJECT_CONTEXT.md` and the relevant files under `/docs` before making changes.

## Mandatory Rules

1. The Project Bible is the source of truth.
2. Human-approved decisions override AI suggestions.
3. Do not invent features.
4. Do not change architecture independently.
5. Do not change API contracts independently.
6. Do not change database schema independently.
7. Stay within your assigned module.
8. Do not modify another member's work unless explicitly authorized.
9. If cross-module work is necessary, report it instead of silently doing it.
10. Keep changes focused.
11. Preserve existing behavior.
12. Do not commit secrets.
13. Run appropriate tests/checks before declaring completion.

## Before Editing
- Read relevant requirements.
- Read architecture.
- Read API/database contracts if applicable.
- Inspect existing code.
- Identify whether the requested change crosses a module boundary.

## When Blocked
Do not invent an answer. Report:
- What is missing.
- Why it is required.
- Which contract/document needs updating.

## Output
At the end, report:
- Files changed.
- What was implemented.
- Tests/checks run.
- Any cross-module dependencies.
- Any unresolved issues.
