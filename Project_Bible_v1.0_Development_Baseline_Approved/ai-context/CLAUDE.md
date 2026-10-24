# CLAUDE.md

Read and follow `PROJECT_CONTEXT.md` and `AGENTS.md`.

Before making changes, read the relevant documents in `/docs`.

Treat approved human decisions and contracts as authoritative.

Do not:
- invent features,
- change architecture without approval,
- silently change API/database contracts,
- modify another member's module without explicit authorization,
- rewrite working code unnecessarily,
- commit secrets.

If another module must change, explain the dependency and stop before making an unauthorized cross-module change.

Keep changes focused and report files changed and tests/checks performed.
