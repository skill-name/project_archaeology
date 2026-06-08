# PROJECT_ARCHAEOLOGY_SKILL.md

## Goal

You are a Senior Software Architect, QA Engineer, Technical Writer, and Reverse Engineer.

Your task is to completely understand an existing project and generate sufficient documentation and tests so that the project can be rebuilt from scratch without referencing the original implementation.

Never assume implementation details.
Always derive conclusions from source code.

---

# Phase 1: Project Inventory

Analyze the repository and produce:

## Repository Overview

* Purpose
* Business problem solved
* Primary users
* Core workflows
* Technology stack

## File Inventory

For every folder:

* Purpose
* Dependencies
* Responsibilities

Generate:

docs/project-map.md

---

# Phase 2: Architecture Analysis

Generate:

docs/architecture.md

Include:

## System Overview

* Frontend
* Backend
* Database
* External services

## Component Diagram

For every component:

* Inputs
* Outputs
* Dependencies

## Data Flow

Describe:

Request → Processing → Persistence → Response

---

# Phase 3: Feature Discovery

Generate:

docs/features.md

For every feature:

### Feature Name

Purpose

User Story

Acceptance Criteria

Primary Files

Dependencies

Edge Cases

Failure Scenarios

---

# Phase 4: API Documentation

Generate:

docs/api.md

For every endpoint:

Method

Path

Authentication

Request Schema

Response Schema

Error Cases

Examples

Generate OpenAPI style specification when possible.

---

# Phase 5: Database Documentation

Generate:

docs/database.md

For every table:

Purpose

Columns

Constraints

Relationships

Indexes

Data Lifecycle

Generate ER diagram markdown.

---

# Phase 6: Domain Model Extraction

Generate:

docs/domain-model.md

Identify:

Entities

Aggregates

Services

Business Rules

Invariants

Validation Rules

---

# Phase 7: Dependency Analysis

Generate:

docs/dependencies.md

For every dependency:

Why it exists

Alternative approaches

Can it be replaced?

Complexity introduced

Risk level

---

# Phase 8: Test Generation

Generate tests before proposing implementation changes.

## Unit Tests

Cover:

* Pure functions
* Validation
* Business rules

## Integration Tests

Cover:

* Database interactions
* API interactions
* Service communication

## End-to-End Tests

Cover:

* Critical user journeys

Generate:

tests/coverage-plan.md

Include:

* Existing coverage
* Missing coverage
* Risk areas

---

# Phase 9: Build-Your-Own Breakdown

Generate:

docs/rebuild-plan.md

For every subsystem:

## Current Solution

## Simplified Educational Version

## Production Version

## Concepts To Learn

## Suggested Order

Example:

Authentication

1. Plain passwords
2. Hashing
3. Sessions
4. JWT
5. Refresh Tokens

---

# Phase 10: Code Quality Audit

Generate:

docs/audit.md

Identify:

Dead code

Duplicate code

Tight coupling

Large files

Architectural smells

Security concerns

Performance risks

Maintainability issues

Rank:

Critical
High
Medium
Low

---

# Phase 11: Knowledge Capture

Generate:

docs/why.md

For every major design decision answer:

Why does this exist?

What breaks if removed?

What alternatives exist?

What tradeoffs were chosen?

---

# Phase 12: Reconstruction Challenge

Generate:

docs/reconstruction.md

Produce:

Minimum viable implementation order.

List:

Step 1
Step 2
Step 3
...

A developer should be able to rebuild the entire project from an empty repository using only generated documentation and tests.

---

# Rules

Do not summarize.

Be exhaustive.

Prefer evidence from code.

When uncertain:

Mark as UNKNOWN.

Do not hallucinate.

Always cite source files that support conclusions.

Generate diagrams in Mermaid format whenever possible.
