# 15 — AI WORKFLOW & CONTEXT MANAGEMENT

## Purpose
Define how the team uses multiple AI tools while maintaining one shared understanding of the product.

## Core Principle

```text
TEAM RESEARCH
     ↓
PROJECT BIBLE
     ↓
HUMAN TEAM APPROVAL
     ↓
GITHUB
     ↓
TEAMMATE PULLS LATEST VERSION
     ↓
PROMPT ARCHITECT
     ↓
ROLE-SPECIFIC PROMPT
     ↓
CODING AI
     ↓
FEATURE BRANCH
     ↓
PULL REQUEST
     ↓
INTEGRATION
```

## Step 1 — Research
The team researches the problem and records evidence in the relevant Project Bible documents.

## Step 2 — Build the Bible
The team fills:
- Problem
- Research
- Users
- Product vision
- Requirements
- User flows
- Feature specifications
- Architecture
- Database
- API contracts
- Design system
- Decisions
- Definition of Done
- Team structure

## Step 3 — Human Approval
Before implementation, the four members review the important documents.

The AI may organize information, but humans approve:
- requirements
- scope
- architecture
- contracts
- team responsibilities

## Step 4 — Push to GitHub
The approved Bible and AI instructions are committed to the same project repository as the application.

## Step 5 — Pull Latest Context
Every teammate starts from the latest approved repository state.

Before generating a task prompt, verify that the local repository is current.

## Step 6 — Generate a Role-Specific Prompt
Use `ai-context/PROMPT_ARCHITECT_MASTER.md`.

Provide:
- coding tool
- team member role
- exact task
- relevant branch/module

The Prompt Architect reads the Project Bible and generates a tool-specific implementation prompt.

## Step 7 — Give Prompt to Coding AI
The teammate gives the generated prompt to their coding AI.

The coding AI must:
- inspect existing code
- follow the Project Bible
- stay within ownership boundaries
- preserve contracts
- avoid scope creep
- report blockers instead of guessing

## Step 8 — Implement on a Feature Branch
Never use AI to make uncontrolled changes directly to the shared main branch.

Recommended pattern:

```text
main
├── feature/frontend-dashboard
├── feature/backend-analysis-api
├── feature/ai-analysis-service
└── feature/database-schema
```

## Step 9 — Review
Before merging:
- inspect the diff
- run tests/checks
- verify contracts
- check for unauthorized files
- verify no secrets were committed

## Step 10 — Integrate
Merge through the project's agreed integration process.

## Bible Change Protocol

If implementation reveals a genuine product/architecture change:

```text
AI discovers issue
      ↓
Report issue
      ↓
Team discusses
      ↓
Human approves decision
      ↓
Update Project Bible
      ↓
Update affected contracts/prompts
      ↓
Continue implementation
```

AI must not silently rewrite the Bible.

## Context Freshness Rule
If requirements, architecture, API contracts, database schema, or design rules change, affected role prompts must be considered stale and regenerated.

## Golden Rule

```text
ONE PROJECT
ONE SOURCE OF TRUTH
MULTIPLE SPECIALIZED AI AGENTS
CONTROLLED OWNERSHIP
HUMAN-APPROVED INTEGRATION
```
