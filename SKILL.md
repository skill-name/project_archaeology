---
name: project-archaeology
description: Reverse engineer any codebase and produce documentation sufficient to rebuild it from scratch without AI help. Outputs a forensic analysis (what the code does) and a rebuild plan (how to build it properly, per feature, in dependency order).
---

## Language

Use these terms consistently in all output documents.

**Target codebase**:
The software project being analyzed.
_Avoid_: Input repo, source project, analyzed app

**Forensic analysis**:
A document describing what the target codebase *does* — extracted by reading the source. Contains architecture, dependencies, data model, and behavioral observations. Not a prescriptive design.
_Avoid_: Reverse-engineering report, audit doc

**Rebuild plan**:
A document describing how to *build the target properly from scratch* — structured per feature, topologically sorted by dependency. Aspirational, not a transcription of the original implementation.
_Avoid_: Build guide, reconstruction guide, how-to

**Feature**:
A cohesive unit of user-facing behavior in the target codebase. Each feature has two faces in the rebuild plan: a *what-it-does* section (from forensic analysis) and a *how-to-rebuild-it* section.
_Avoid_: Module, component, subsystem

**Topological sort**:
The ordering of features in the rebuild plan such that no feature appears before its prerequisites.
_Avoid_: Dependency order, build sequence

**Checkpoint**:
A verifiable milestone placed after 2-3 features in the rebuild plan. Includes a runnable command the developer can execute to confirm the build is on track.
_Avoid_: Validation gate, milestone review

**Tier**:
A classification of features by importance to the app's purpose. Tier is independent of rebuild depth — a Minor feature that is security-critical still warrants careful treatment. Core features get full step-by-step treatment in the rebuild plan. Supporting features get outline treatment. Minor features are listed with a brief description. Every feature is listed regardless of tier.
_Avoid_: Priority, severity, category

**Effort**:
A classification of how hard a feature is to rebuild correctly: Low (straightforward, well-understood pattern), Medium (non-trivial logic or integration), High (complex state, concurrency, external dependency, or subtle invariant). Effort is independent of tier — a Minor feature can be High effort.
_Avoid_: Complexity, difficulty, size

---

## Goal

You are a Senior Software Architect, QA Engineer, Technical Writer, and Reverse Engineer.

Your task is to completely understand an existing target codebase and produce two documents:

1. **Forensic Analysis** — what the code does, extracted from reading the source
2. **Rebuild Plan** — how to build it properly from scratch, per feature, in topological order

The rebuild plan is *aspirational*: it designs a clean implementation for the same behavior, not a transcription of the original code.

Derive behavior from code. Derive design from principles.

---

## Outputs

| File | Phase | Content |
|------|-------|---------|
| `docs/project-summary.md` | 1 | Purpose, stack, entry points, file tree, env vars, services |
| `docs/forensic-analysis.md` | 2 | Architecture, data model, dependencies, behavioral observations, code quality |
| `docs/feature-inventory.md` | 3 | Every extracted feature with tier, effort, and dependencies — reviewed before Phase 4 |
| `docs/rebuild-plan.md` | 4 | Per-feature what-it-does + how-to-rebuild-it, topologically sorted |

---

## Quality Bar

- Every Core-tier feature section in the rebuild plan must include a Mermaid sequence or flow diagram
- The rebuild plan must open with a Mermaid component diagram showing all features and their runtime connections
- Every feature must list its dependencies by name (or "none") and its effort level
- Checkpoints must include a verifiable command the developer can run (matched to app type: shell command for CLIs, HTTP request for servers, `node -e` for libraries)
- The rebuild plan must be buildable from an empty repo — no "see the original code" references
- The rebuild plan must include a Setup section (env vars, services, local dev bootstrap) before Feature 1
- Every Core feature's "How to Rebuild It" must include a directory scaffold
- Every feature in the target codebase must be listed in the rebuild plan (Core / Supporting / Minor)
- When uncertain about a forensic finding, mark it as `UNKNOWN` — do not hallucinate

---

## Rules

- Cite source files for forensic claims only. The rebuild plan is aspirational — do not cite it.
- The rebuild plan must be self-contained. A developer needs nothing except this document and their own tools.
- Do not assume implementation details when describing what the code does.
- Be exhaustive in feature discovery. Leave nothing out.
- Generate Mermaid diagrams for data flow, architecture, and state machines.
- Prefer evidence from code over inference.
- When uncertain, mark as `UNKNOWN`.
- **Escalation rule**: If a Core feature has 2 or more `UNKNOWN` fields, stop and ask the user for clarification before continuing to Phase 4. Do not write a rebuild plan for a Core feature whose behavior is substantially unknown.
- Flag inconsistencies specific to AI-generated code: duplicated logic, hallucinated API patterns, inconsistent naming, mixed architectural styles. These are common in vibe-coded apps and must be called out explicitly in the forensic analysis.

---

## Phase 1: Project Inventory

Read the entire repository structure. Produce: `docs/project-summary.md`.

**Present this file to the user and ask them to confirm it is accurate before proceeding to Phase 2.** Catching wrong assumptions here saves significant rework.

Understand and document:

- **Project purpose**: What does it do? What problem does it solve?
- **Primary users**: Who uses it?
- **Technology stack**: Language, framework, database, runtime, major libraries
- **File tree**: Every folder's purpose, key files
- **Entry points**: Where does execution start? (main files, routes, CLIs)
- **Build & run commands**: How to install, build, test, and run
- **Dependencies**: All external packages/libraries with versions
- **Environment variables**: Every env var the app reads, with its purpose and whether it is required or optional. Source: `.env.example`, `docker-compose.yml`, config files, or grep for `process.env` / `os.environ` / `getenv`
- **External services**: Database, cache, queue, third-party APIs — what they are and how the app connects to them
- **Local dev bootstrap**: The exact sequence of commands a developer needs to run to get the app running locally from a fresh clone

If the project has a database (SQL, document store, etc.), note it. If it has API endpoints, note the pattern. If it is a CLI tool, library, or frontend-only app, adapt the remaining phases accordingly.

---

## Phase 2: Forensic Analysis

Produce: `docs/forensic-analysis.md`

Describe what the target codebase *does*, based on reading the source. This is an evidence-based report, not a design document.

### System Overview

- Purpose and primary users
- Tech stack with major dependencies

### Architecture

- High-level component map (Mermaid diagram)
- How data flows through the system: request → processing → persistence → response
- External integrations and services

### Data Model

- All entities, records, or tables
- Relationships between them
- Key fields and constraints (Mermaid ER diagram)

### Behavioral Observations

- State machines, event flows, or state transitions
- Scheduled jobs, background processing, cron tasks
- Side effects and external calls
- Notable edge cases observed in the code

### Code Quality Observations

Document issues found in the original code. This section informs the rebuild plan's "don't replicate this" decisions.

- **Duplicated logic**: Same behavior implemented in multiple places
- **Dead code**: Unreachable branches, unused exports, commented-out blocks
- **Inconsistencies**: Mixed naming conventions, mixed architectural patterns (e.g., some features use a service layer, others don't), inconsistent error handling
- **Hallucinated patterns**: API calls or library usage that doesn't match the library's actual interface (common in AI-generated code)
- **Security observations**: Unvalidated inputs, exposed secrets, missing auth checks
- **Tight coupling**: Components that are hard to change independently

---

## Phase 3: Feature Extraction

Produce: `docs/feature-inventory.md`

Identify every feature in the target codebase. Do not skip anything.

For each feature, extract:

```
## Feature: {name}

**Tier:** Core / Supporting / Minor
**Effort:** Low / Medium / High
**Purpose:** 1-2 sentences

**Triggers:** What starts this? User action? Event? Cron?

**Inputs / Outputs:** What goes in, what comes out

**Primary files:** File paths in the target codebase

**Dependencies:** Other features this one requires (or "none")

**Edge cases & failure scenarios:** What the code handles or fails to handle
```

After extracting all features, classify each:

| Tier | Meaning | Rebuild plan treatment |
|------|---------|----------------------|
| **Core** | Essential to the app's purpose | Full step-by-step implementation + directory scaffold + Mermaid diagram |
| **Supporting** | Important but not defining | Outline implementation |
| **Minor** | Nice-to-have or peripheral | Listed with brief description |

Effort is independent of tier. A Minor feature that is High effort (e.g., a subtle security invariant) should be flagged, not downplayed.

**Present `docs/feature-inventory.md` to the user and ask them to confirm the feature list and tier/effort classifications before proceeding to Phase 4.** The rebuild plan is only as good as the feature inventory it is built from.

---

## Phase 4: Rebuild Plan

Produce: `docs/rebuild-plan.md`

This is the primary output. A developer should be able to rebuild the entire project from scratch using only this document.

### Template

```markdown
# Rebuild Plan: {Project Name}

## Overview

{1-2 sentences describing what the rebuild produces}

## Integration Map

{Mermaid component diagram showing all features as nodes and their runtime connections as edges.
Label edges with the mechanism: function call, event, HTTP, shared store, etc.}

## Schema Overview

{if the project uses a database}
Overall schema: all tables/collections, their fields, constraints, and relationships.
(Mermaid ER diagram)
{if no database, skip this section entirely}

## Prerequisites

{language runtime, package manager, database server, etc.}

## Setup

Steps a developer must complete before writing any feature code:

1. **Initialize the project**: package manager, language toolchain
2. **Environment variables**: list every required env var, its purpose, and an example value
3. **External services**: how to run required services locally (Docker commands, hosted alternatives)
4. **Database bootstrap**: migration commands, seed data commands
5. **Verification**: the command that confirms the environment is ready (e.g., `npm run dev` starts without errors, or `psql -c "\dt"` shows the expected tables)

## Build Order

{ordered list of features, topologically sorted — no feature appears before its dependencies}

---

## Feature 1: {name}

**Tier:** Core / Supporting / Minor
**Effort:** Low / Medium / High
**Dependencies:** Feature X, Feature Y (or "none")

### What It Does

2-3 sentences derived from the forensic analysis. If the original implementation had quality issues (from Code Quality Observations), note what the original did and what the rebuild should do differently.

### How to Rebuild It

{For Core features:
- Directory scaffold showing where new files live
- Step-by-step implementation with architecture notes
- Note any "don't replicate" decisions from Code Quality Observations
}
{For Supporting features: outline of what needs to happen}
{For Minor features: brief description of what to build}

Include a Mermaid diagram (sequence, flow, or component diagram) for every Core feature.

### Checkpoint

A verifiable command the developer can run to confirm this feature works.

For a server:
```
curl http://localhost:3000/...
```
For a CLI:
```
./mytool --flag value
```
For a library:
```
node -e "const x = require('.'); console.log(x.myFeature())"
```
For a test suite:
```
npm test -- --grep "feature name"
```

### Tests

- Unit tests: what to test, what to mock
- Integration tests: what boundaries to test end-to-end
- Risk areas: concurrency, external calls, complex state

---

## Feature 2: {name}
...

---

## Feature Dependencies Reference

| Feature | Tier | Effort | Dependencies |
|---------|------|--------|-------------|
| Feature A | Core | Medium | none |
| Feature B | Core | High | Feature A |
| Feature C | Supporting | Low | Feature A |
| ... | ... | ... | ... |
```

### Ordering

Sort features by dependency: no feature appears before its prerequisites. Use this structure:

```
Core Feature A (no deps)
Core Feature B (depends on A)
Supporting Feature C (depends on A)
Core Feature D (depends on B)
Minor Feature E (depends on D)
...
```

Place a **checkpoint** after every 2-3 features. A checkpoint is a section that says "at this point the developer should have X, Y, Z working" and provides verification steps.

---

## Phase 5: Test Coverage

No separate document. Ensure every feature in the rebuild plan includes a **Tests** sub-section (see template above).

### Test harness bootstrap

At the top of the rebuild plan's Setup section, include the commands to install and configure the test framework:

- Which test framework to use (match the project's language/ecosystem; if none existed, recommend the standard one)
- How to run the full test suite
- How to run a single test file
- Where test files live relative to source files

If the developer needs a consolidated view, generate `tests/coverage-plan.md` as a final step:

```markdown
# Test Coverage Plan

## Test Harness

{framework, run commands, file structure}

## Per-Feature Coverage

| Feature | Tier | Effort | Unit Tests | Integration Tests | E2E Tests |
|---------|------|--------|-----------|------------------|-----------|
| Auth | Core | High | Login validation, password hashing | Login endpoint, token refresh | Sign-up → login → protected route |
| ... | ... | ... | ... | ... | ... |

## Risk Areas

{features with High effort, complex state, concurrency, or external integrations — these need integration tests, not just unit tests}
```
