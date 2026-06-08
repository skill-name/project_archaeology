---
name: project-archaeology
description: Reverse engineer any codebase and produce documentation sufficient to rebuild it from scratch without AI help. Outputs a forensic analysis (what the code does) and a portable rebuild plan (how to build it properly, per feature, in dependency order).
---

## Glossary

Use these terms consistently across all output documents.

| Term | Definition | Avoid |
|------|-----------|-------|
| **Target codebase** | The software project being analyzed | Input repo, source project |
| **Forensic analysis** | Evidence-based document describing what the target codebase *does*, extracted by reading the source | Reverse-engineering report, audit doc |
| **Rebuild plan** | Aspirational document describing how to build the same system properly from scratch, per feature, in topological order. Not a transcription of the original | Build guide, reconstruction guide |
| **Feature** | A cohesive unit of user-facing behavior | Module, component, subsystem |
| **Topological sort** | Feature ordering where no feature appears before its prerequisites | Dependency order, build sequence |
| **Checkpoint** | Verifiable milestone after 2–3 features; includes a runnable command to confirm progress | Validation gate, milestone review |
| **Tier** | Feature classification by importance: Core (essential), Supporting (important), Minor (peripheral). Independent of effort | Priority, severity |
| **Effort** | How hard a feature is to rebuild: Low, Medium, High. Independent of tier | Complexity, difficulty |

---

## Goal

You are a Senior Software Architect, QA Engineer, Technical Writer, and Reverse Engineer.

Run all five phases sequentially in a single uninterrupted pass. Do not pause between phases, do not ask for confirmation, do not wait for user input. Produce all output files and deliver them at the end.

The central constraint: **`docs/rebuild-plan.md` must be fully portable.** A developer who copies only that file to a machine with no access to the original codebase must be able to rebuild the entire system. Every business rule, API contract, data shape, validation constraint, and algorithm extracted from the original must be stated inline in the rebuild plan — never referenced externally.

Derive behavior from code. Derive design from principles.

---

## Outputs

| File | Phase | What it contains |
|------|-------|-----------------|
| `docs/project-summary.md` | 1 | Purpose, stack, file tree, entry points, env vars, external services, local dev bootstrap |
| `docs/forensic-analysis.md` | 2 | Architecture, data model, API contracts, validation rules, algorithms, behavioral observations, code quality |
| `docs/feature-inventory.md` | 3 | Every feature with tier, effort, dependencies, triggers, inputs/outputs, edge cases |
| `docs/rebuild-plan.md` | 4 | Fully portable standalone spec: setup, business rules, API contracts, data shapes, per-feature build instructions, tests |

---

## Quality Bar

- `docs/rebuild-plan.md` is self-sufficient: no section references the forensic analysis, feature inventory, or any file path from the original codebase
- Every API contract (method, path, request/response shapes, error codes) appears verbatim in the rebuild plan
- Every validation rule, business constraint, and domain invariant is stated explicitly in plain language
- Every Core feature includes a Mermaid sequence or flow diagram
- The rebuild plan opens with a Mermaid integration map of all features and their runtime connections
- Every feature lists its dependencies and effort level
- Checkpoints use commands appropriate to the app type (HTTP, CLI, library, test runner)
- Every Core feature's "How to Rebuild It" includes a directory scaffold
- Every feature in the target codebase is listed — nothing skipped
- Uncertainty is marked `UNKNOWN`, never fabricated

---

## Rules

1. **Portability**: Before writing any rebuild plan section, ask: "Can a developer on a new machine, with only this file, build this?" If no, expand until yes. No source file paths, no "see the forensic analysis", no "refer to the original".

2. **Extract, don't refer**: Every business rule, validation constraint, data shape, API contract, and algorithm observed in the original must be transcribed into the rebuild plan in plain language.

3. **Source citations belong only in the forensic analysis.** The rebuild plan is aspirational — it cites nothing from the original.

4. **Be exhaustive in feature discovery.** No feature is too small to list.

5. **Prefer evidence from code over inference.** When uncertain, mark `UNKNOWN`.

6. **Escalation**: If a Core feature has 2 or more `UNKNOWN` fields, continue the run anyway. Write the rebuild plan with unknowns marked. Collect all unresolved unknowns in a **Blockers** table at the end of `docs/rebuild-plan.md`.

7. **Flag AI-generated code patterns**: Duplicated logic, hallucinated library APIs, inconsistent naming, mixed architectural styles — these are common in vibe-coded apps and must be called out explicitly in the forensic analysis and corrected in the rebuild plan.

8. **Generate Mermaid diagrams** for architecture, data flow, and state machines.

---

## Phase 1 — Project Inventory

**Produces:** `docs/project-summary.md` → proceed immediately to Phase 2.

Capture:

- **Purpose**: What does it do? What problem does it solve?
- **Primary users**: Who uses it?
- **Tech stack**: Language, framework, database, runtime, major libraries with versions
- **File tree**: Every folder's purpose, key files
- **Entry points**: Where execution starts (main files, routes, CLI commands)
- **Build & run commands**: Install, build, test, run
- **Dependencies**: All external packages with versions
- **Environment variables**: Every env var the app reads — purpose, required/optional, default. Source from `.env.example`, `docker-compose.yml`, or grep for `process.env` / `os.environ` / `getenv`
- **External services**: Database, cache, queue, third-party APIs — what they are and how the app connects
- **Local dev bootstrap**: Exact command sequence from fresh clone to running app

---

## Phase 2 — Forensic Analysis

**Produces:** `docs/forensic-analysis.md` → proceed immediately to Phase 3.

This is an evidence-based report. Cite source files for every claim. Do not prescribe design.

### System Overview
- Purpose, primary users, tech stack with major dependencies

### Architecture
- High-level component map (Mermaid diagram)
- Data flow: request → processing → persistence → response
- External integrations and services

### Data Model
- All entities, tables, or collections
- Relationships and key constraints (Mermaid ER diagram)
- Field types, nullability, uniqueness

### API & Interface Contracts
For every HTTP endpoint, CLI command, or public function:
- Method + path (or command + flags)
- Authentication requirement
- Request: every field, type, required/optional
- Response: every field, type, per status code
- All error codes and what triggers each

### Validation Rules & Business Constraints
Every rule the system enforces, stated as assertions:
- Field-level: type, format, length, range, enum values
- Cross-field: conditional dependencies
- Domain-level: invariants (user can only have one active X, Y must happen before Z)

### Domain Algorithms
Any non-trivial computation: scoring, ranking, pricing, permission checks, state transitions. Describe precisely enough to reimplement without reading the original.

### Behavioral Observations
- State machines and transitions
- Scheduled jobs, background tasks
- Side effects and external calls
- Notable edge cases the code handles or fails to handle

### Code Quality Observations
Issues that the rebuild plan should correct:
- **Duplicated logic**: Same behavior implemented in multiple places
- **Dead code**: Unreachable branches, unused exports, commented-out blocks
- **Inconsistencies**: Mixed naming conventions, mixed architectural patterns, inconsistent error handling
- **Hallucinated patterns**: Library or API usage that doesn't match the actual interface
- **Security gaps**: Unvalidated inputs, exposed secrets, missing auth checks
- **Tight coupling**: Components that can't change independently

---

## Phase 3 — Feature Extraction

**Produces:** `docs/feature-inventory.md` → proceed immediately to Phase 4.

Identify every feature. Do not skip anything.

For each feature:

```
## Feature: {name}

**Tier:** Core / Supporting / Minor
**Effort:** Low / Medium / High
**Purpose:** 1–2 sentences

**Triggers:** What starts this? User action, event, cron, startup?

**Inputs / Outputs:** What goes in, what comes out — with types

**Primary files:** File paths in the target codebase

**Dependencies:** Other features required before this one (or "none")

**Edge cases & failure scenarios:** What the code handles or fails to handle
```

Tier and effort are independent. A Minor feature can be High effort (e.g., a subtle security invariant). Flag any such combination explicitly.

| Tier | Meaning | Rebuild plan depth |
|------|---------|-------------------|
| **Core** | Essential to the app's purpose | Full step-by-step + directory scaffold + Mermaid diagram |
| **Supporting** | Important but not defining | Outline |
| **Minor** | Peripheral or nice-to-have | One-paragraph description |

---

## Phase 4 — Rebuild Plan

**Produces:** `docs/rebuild-plan.md`

This document must stand alone. Structure it as follows:

---

### `docs/rebuild-plan.md` structure

#### Header
```
# Rebuild Plan: {Project Name}

{1–2 sentences: what this document produces when followed}
```

#### Integration Map
Mermaid component diagram. Every feature is a node. Edges are labeled with the runtime connection mechanism: function call, HTTP, event, shared store, etc.

#### Schema Overview *(skip if no database)*
Mermaid ER diagram with all tables/collections, fields, types, and relationships.

#### Prerequisites
Language runtime, package manager, database server, and any other required tools — with version requirements.

#### Setup
Steps before writing any feature code:

1. Initialize the project (package manager, toolchain)
2. Environment variables — list every required var, its purpose, and an example value
3. External services — how to run locally (Docker commands or hosted alternatives)
4. Test framework — install command, how to run all tests, how to run one file, where test files live
5. Database bootstrap — migration command, seed data command
6. Verification — the command that confirms the environment is ready

#### Business Rules & Data Contracts
The source of truth for all interfaces and constraints. Written once here; feature sections cross-reference it. A developer must never need the original codebase to understand what to implement.

**API Contracts** — for every endpoint or public interface:
```
POST /api/example
  Auth:     Bearer token / None
  Request:  { field: type (required), field: type (optional) }
  200:      { field: type }
  401:      { error: "unauthorized" }
  422:      { error: string, field?: string }
```
For CLIs: document every command, its flags, and its stdout/stderr contract.

**Data Shapes** — the exact shape of every object that crosses a feature boundary:
```
User:    { id: uuid, email: string, role: "admin"|"user", createdAt: timestamp }
Session: { token: string, userId: uuid, expiresAt: timestamp }
```

**Validation Rules & Business Constraints** — stated as boolean assertions:
- `email` must match RFC 5322 format
- `password` must be ≥ 8 characters with ≥ 1 uppercase and ≥ 1 digit
- (every constraint from the original, stated explicitly)

**Domain Algorithms** — non-trivial computations described step-by-step in plain language, sufficient to reimplement from scratch.

#### Build Order
Ordered list of features, topologically sorted. No feature appears before its prerequisites.

#### Features (one section per feature, sorted per Build Order)

Each feature follows this template:

```
## Feature N: {name}

**Tier:** Core / Supporting / Minor
**Effort:** Low / Medium / High
**Dependencies:** Feature X, Feature Y (or "none")

### What It Does

Fully self-contained description. No references to the original code or forensic analysis.
Include:
- Behavior in plain language
- Inputs with exact types and constraints (cite Business Rules & Data Contracts)
- Outputs with exact shapes
- All business rules and validation enforced
- All side effects (DB writes, emails, events, cache updates)
- Error conditions and how each is handled
- If the original had a quality issue: state what was wrong and what the correct behavior is

### How to Rebuild It

[Core] Directory scaffold + step-by-step implementation + Mermaid diagram + any "don't replicate" notes
[Supporting] Outline of what needs to happen
[Minor] Brief description of what to build

### Checkpoint

Command to verify this feature works, matched to app type:

  Server:  curl -X POST http://localhost:3000/api/example -d '{"field":"value"}'
  CLI:     ./mytool --flag value
  Library: node -e "const x = require('.'); console.log(x.myFeature())"
  Tests:   npm test -- --grep "feature name"

### Tests

- Unit: what logic to test in isolation, what to mock
- Integration: what cross-boundary behavior to test end-to-end
- Risk areas: concurrency, external calls, complex state
```

Place a **checkpoint section** after every 2–3 features:

```
## Checkpoint: {Features N–M}

At this point the developer should have working: X, Y, Z.

Verify:
  {command}  → expected output
  {command}  → expected output
```

#### Feature Dependencies Reference

| Feature | Tier | Effort | Dependencies |
|---------|------|--------|-------------|
| Feature A | Core | Medium | none |
| Feature B | Core | High | Feature A |
| Feature C | Supporting | Low | Feature A |

#### Blockers

Every `UNKNOWN` field that could not be resolved during analysis:

| Feature | Field | What is unknown | How to investigate |
|---------|-------|-----------------|-------------------|
| {name} | {field} | {what is unclear} | {specific question or check to resolve it} |

If there are no blockers: *"None — all features were fully understood."*

---

## Phase 5 — Test Coverage

No separate document. Tests are written inline per feature in `docs/rebuild-plan.md` (see the **Tests** sub-section in each feature template above).

If a consolidated view is needed, produce `docs/tests/coverage-plan.md`:

```
# Test Coverage Plan

## Test Harness
{framework, install command, run-all command, run-one command, file location convention}

## Per-Feature Coverage

| Feature | Tier | Effort | Unit Tests | Integration Tests | E2E Tests |
|---------|------|--------|-----------|------------------|-----------|
| ...     | ...  | ...    | ...       | ...              | ...       |

## Risk Areas
Features that need integration tests, not just unit tests:
{High-effort features, features with concurrency, external calls, or complex state transitions}
```
