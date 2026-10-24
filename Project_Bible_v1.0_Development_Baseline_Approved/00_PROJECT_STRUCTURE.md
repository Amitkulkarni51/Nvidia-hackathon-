# HACKATHON AI PROJECT — MASTER STRUCTURE

## Purpose
This repository is the single source of truth for the hackathon team, humans, and AI coding agents.

## Repository Structure

```text
hackathon-project/
│
├── docs/                              # Project Bible and approved documentation
│   ├── 01_problem_statement.md
│   ├── 02_research.md
│   ├── 03_users_and_personas.md
│   ├── 04_product_vision.md
│   ├── 05_requirements.md
│   ├── 06_user_flows.md
│   ├── 07_feature_specs.md
│   ├── 08_architecture.md
│   ├── 09_database.md
│   ├── 10_api_contract.md
│   ├── 11_design_system.md
│   ├── 12_decisions.md
│   └── 13_definition_of_done.md
│
├── ai-context/                        # AI operating instructions
│   ├── AGENTS.md
│   ├── CLAUDE.md
│   ├── PROJECT_CONTEXT.md
│   └── prompts/
│       ├── frontend.md
│       ├── backend.md
│       ├── ai.md
│       ├── database.md
│       └── integration.md
│
├── frontend/                          # Frontend implementation
│   └── AGENTS.md
│
├── backend/                           # Backend implementation
│   └── AGENTS.md
│
├── ai/                                # AI/ML implementation
│   └── AGENTS.md
│
├── database/                          # Schema and migrations
│   └── AGENTS.md
│
├── tests/
├── README.md
├── .env.example
└── .gitignore
```

## Source-of-Truth Hierarchy

1. Approved human/team decisions
2. `docs/` Project Bible
3. `ai-context/` generated/shared AI instructions
4. Role-specific prompts
5. Source code

AI agents must not override an approved requirement or architecture decision.

## Core Rule
The repository is the shared context. Do not maintain a separate unofficial version of the product definition.
