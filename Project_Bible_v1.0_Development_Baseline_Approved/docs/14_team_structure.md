# 14 — TEAM STRUCTURE & OWNERSHIP

## Purpose
Define exactly who owns each project area so humans and AI agents do not duplicate or overwrite work.

## Team
| Member | Role | Primary Module | Primary AI Tool | Backup/Secondary Tool | Responsibilities |
|---|---|---|---|---|---|
| Member 1 | Integration / Lead | Shared integration | [Tool] | [Tool] | Architecture consistency, integration, final coordination |
| Member 2 | Frontend | `/frontend` | [Lovable/etc.] | [Tool] | UI, pages, user flows, frontend API integration |
| Member 3 | Backend | `/backend` | [Claude Code/etc.] | [Tool] | APIs, business logic, authentication |
| Member 4 | AI / Database | `/ai`, `/database` | [Cursor/etc.] | [Tool] | AI service, data layer, schema |

Replace placeholders with actual team assignments before implementation.

## Ownership Rules
- Each member owns their assigned module.
- Ownership means primary responsibility, not exclusive access to the repository.
- AI agents must stay within the assigned module unless a cross-module change is explicitly approved.
- No member should silently rewrite another member's implementation.
- Shared files require coordination.
- API, database, architecture, and other contracts must not be changed silently.

## Responsibility Boundaries
### Integration Owner
- Maintains integration consistency.
- Reviews cross-module changes.
- Coordinates contract changes.
- Ensures the final product works as one system.

### Module Owners
Each module owner:
- Understands the relevant Project Bible documents.
- Uses the approved role-specific AI prompt.
- Works primarily in the assigned module.
- Reports cross-module dependencies.
- Keeps their branch focused.

## Changes to Ownership
Any reassignment must be recorded here and communicated to the team.
