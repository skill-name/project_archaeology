---
name: project-archaeology
description: Reverse engineer any codebase and produce documentation sufficient to rebuild it from scratch without AI help. Outputs a forensic analysis (what the code does) and a build-your-own-X style tutorial that interleaves understanding and implementation step by step.
---

## Glossary

| Term | Definition | Avoid |
|------|-----------|-------|
| **Target codebase** | The software project being analyzed | Input repo, source project |
| **Forensic analysis** | Evidence-based document describing what the target codebase *does*, extracted by reading the source | Reverse-engineering report, audit doc |
| **Rebuild tutorial** | Step-by-step tutorial that rebuilds the same system from scratch. Understanding and implementation are interleaved — each step explains a concept then immediately implements it. Reads like build-your-own-X, not a spec | Build guide, rebuild plan, spec |
| **Feature** | A cohesive unit of user-facing behavior, implemented as a self-contained tutorial chapter | Module, component, subsystem |
| **Step** | A single concept-then-implement unit within a feature. Each step ends with something runnable. | Task, sub-task |
| **Topological sort** | Feature ordering where no feature appears before its prerequisites | Dependency order, build sequence |
| **Checkpoint** | Runnable verification at the end of a feature confirming it works end-to-end | Validation gate, milestone |
| **Tier** | Feature importance: Core (essential), Supporting (important), Minor (peripheral). Independent of effort | Priority, severity |
| **Effort** | How hard a feature is to build correctly: Low, Medium, High. Independent of tier | Complexity, difficulty |

---

## Goal

You are a Senior Software Architect, QA Engineer, Technical Writer, and Reverse Engineer.

Run all five phases in a single uninterrupted pass. Do not pause, do not ask for confirmation, do not wait for input. Produce all output files and deliver them at the end.

The central constraint: **`docs/rebuild-tutorial.md` must be fully portable and self-teaching.** A developer who copies only that file to a machine with no access to the original codebase must be able to understand and rebuild the entire system by following it top to bottom. Every business rule, API contract, data shape, validation constraint, and algorithm must be explained inline, at the moment it is needed — not in a separate reference section that the developer must consult separately.

The tutorial voice is second person ("you"). Concepts are introduced just-in-time, immediately before the step that needs them. Every step produces something runnable.

Derive behavior from code. Derive design from principles.

---

## Outputs

| File | Phase | What it contains |
|------|-------|-----------------|
| `docs/project-summary.md` | 1 | Purpose, stack, file tree, entry points, env vars, external services, local dev bootstrap |
| `docs/forensic-analysis.md` | 2 | Architecture, data model, API contracts, validation rules, algorithms, behavioral observations, code quality |
| `docs/feature-inventory.md` | 3 | Every feature with tier, effort, dependencies, triggers, inputs/outputs, edge cases |
| `docs/rebuild-tutorial.md` | 4 | Fully portable build-your-own-X tutorial: setup, then one chapter per feature, understanding and implementation interleaved |

---

## Quality Bar

- `docs/rebuild-tutorial.md` is self-sufficient: no sentence references the forensic analysis, the feature inventory, or any file path from the original codebase
- Every API contract (method, path, request/response shapes, error codes) is explained inline at the step that implements it
- Every validation rule and business constraint is explained inline at the step that enforces it
- Every Core feature chapter includes a Mermaid sequence or flow diagram introduced at the point it aids understanding
- The tutorial opens with a Mermaid integration map of all features and their runtime connections
- Every feature chapter lists dependencies and effort
- Every step ends with a runnable verification command
- Every Core feature chapter has a directory scaffold showing where new files live
- Every feature in the target codebase is covered — nothing skipped
- Uncertainty is marked `UNKNOWN`, never fabricated

---

## Rules

1. **Portability**: Before writing any section, ask: "Can a developer on a new machine, with only this file, understand and build this?" If no, expand until yes.

2. **Interleave, don't separate**: Do not write a "theory block" followed by a "code block". Explain a concept in 1–3 sentences, then show the implementation immediately. The reader learns by doing, not by reading first.

3. **Just-in-time concepts**: Introduce a business rule, data shape, or constraint exactly at the step that needs it — not in a glossary or reference section at the top of the feature.

4. **Every step is runnable**: Each step within a feature must end with something the developer can execute and observe. If a step produces no observable output, it should be merged with the next step.

5. **Source citations belong only in the forensic analysis.** The rebuild tutorial cites nothing from the original.

6. **Extract, don't refer**: Every business rule, API contract, data shape, algorithm, and constraint observed in the original must be transcribed into the tutorial in plain language at the relevant step.

7. **Be exhaustive in feature discovery.** No feature is too small to include.

8. **Prefer evidence from code over inference.** When uncertain, mark `UNKNOWN`.

9. **Escalation**: If a Core feature has 2 or more `UNKNOWN` fields, continue the run. Write the tutorial with unknowns marked. Collect all unresolved unknowns in a **Blockers** section at the end of `docs/rebuild-tutorial.md`.

10. **Flag AI-generated code patterns**: Duplicated logic, hallucinated library APIs, inconsistent naming, mixed architectural styles are common in vibe-coded apps. Call them out in the forensic analysis and correct them in the tutorial — explain what the original did wrong and why the tutorial's approach is better.

11. **Mermaid diagrams** for architecture, data flow, and state machines — placed where they aid comprehension, not at fixed locations.

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

Evidence-based report. Cite source files for every claim. Do not prescribe design.

### System Overview
Purpose, primary users, tech stack with major dependencies.

### Architecture
High-level component map (Mermaid diagram). Data flow: request → processing → persistence → response. External integrations.

### Data Model
All entities, tables, or collections. Relationships and key constraints (Mermaid ER diagram). Field types, nullability, uniqueness.

### API & Interface Contracts
For every HTTP endpoint, CLI command, or public function:
- Method + path (or command + flags)
- Authentication requirement
- Request: every field, type, required/optional
- Response: every field, type, per status code
- All error codes and what triggers each

### Validation Rules & Business Constraints
Every rule the system enforces, stated as concrete assertions:
- Field-level: type, format, length, range, enum values
- Cross-field: conditional dependencies
- Domain-level: invariants (user can only have one active X, Y must precede Z)

### Domain Algorithms
Any non-trivial computation: scoring, ranking, pricing, permission checks, state transitions. Described precisely enough to reimplement without reading the original.

### Behavioral Observations
- State machines and transitions
- Scheduled jobs, background tasks
- Side effects and external calls
- Notable edge cases the code handles or fails to handle

### Code Quality Observations
Issues that the tutorial should correct:
- **Duplicated logic**: Same behavior in multiple places
- **Dead code**: Unreachable branches, unused exports
- **Inconsistencies**: Mixed naming, mixed patterns, inconsistent error handling
- **Hallucinated patterns**: Library/API usage that doesn't match the actual interface
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

Tier and effort are independent. A Minor feature can be High effort. Flag any such combination.

| Tier | Meaning | Tutorial depth |
|------|---------|---------------|
| **Core** | Essential to the app's purpose | Full step-by-step chapter with Mermaid diagram and directory scaffold |
| **Supporting** | Important but not defining | Condensed chapter with key steps |
| **Minor** | Peripheral or nice-to-have | Short section: what to build and one verification command |

---

## Phase 4 — Rebuild Tutorial

**Produces:** `docs/rebuild-tutorial.md`

Write in second person ("you"). Teach through doing. Introduce every concept at the moment it is needed, then implement it immediately. Every step ends with something runnable. Never reference the original codebase or any other document.

---

### `docs/rebuild-tutorial.md` structure

#### Opening

```
# Build Your Own {Project Name}

{2–3 sentences: what you will build, what it does, who it is for.}

By the end of this tutorial you will have a working {project name} built from scratch.
```

#### What You're Building
Mermaid integration map — all features as nodes, edges labeled with runtime connection mechanism (function call, HTTP, event, shared store). This gives the reader the full picture before they write a single line.

#### Prerequisites
Language runtime, package manager, database server, and any other tools with version requirements.

#### Setup

Walk the developer through the exact steps to get a working empty project:

1. Initialize the project (package manager, toolchain, repo)
2. Set up environment variables — for each var: what it does, example value, how to set it
3. Start external services — Docker commands or hosted alternatives
4. Install and configure the test framework — run-all command, run-one command, file location convention
5. Bootstrap the database — migration command, seed command
6. Verify — the single command that confirms everything is ready

End with: *"Run `{command}`. You should see `{expected output}`. If so, you're ready to build."*

#### Feature Chapters (one per feature, in topological order)

Each chapter follows this template:

---

```
## Chapter N: {Feature Name}

**Tier:** Core / Supporting / Minor
**Effort:** Low / Medium / High
**Depends on:** Chapter X, Chapter Y (or "none")

{Opening paragraph: what this feature does and why it exists in the system.
Second person. 2–4 sentences. Do not summarize — tell the reader what they are about
to build and why it matters.}

{If this feature has a non-trivial flow, include a Mermaid sequence or flow diagram here,
introduced with a sentence like "Here's how the pieces fit together:"}

### Step 1: {Verb phrase describing what you build in this step}

{Explain the concept behind this step in 1–3 sentences. Introduce any business rule,
data shape, API contract, or constraint that this step implements — stated as a fact,
not a reference to another document.

Example: "A session token is a signed JWT containing the user's ID and role.
It expires after 24 hours. The signing secret comes from the SESSION_SECRET env var."}

{Implementation: schema DDL, code structure, config snippet, or commands.
Be specific enough that the developer knows exactly what to write.}

**Verify:**
{command} → {expected output or observable behavior}

### Step 2: {Verb phrase}

{concept explanation}

{implementation}

**Verify:**
{command} → {expected output}

...repeat for all steps...

### Checkpoint

You now have a working {feature name}. Run the full verification:

{command} → {expected output}
{command} → {expected output}

### Tests

**Unit tests** — test these behaviors in isolation:
- {behavior}: {what to assert, what to mock}

**Integration tests** — test these boundaries end-to-end:
- {boundary}: {what to exercise, what to assert}

**Risk areas**: {anything in this feature with concurrency, external calls, or complex state
that needs extra test coverage}
```

---

Place a **milestone section** after every 2–3 chapters:

```
---

## Milestone: {Chapters N–M} Complete

At this point your app can:
- {capability 1}
- {capability 2}

Run all of the following and confirm they pass:

{command} → {expected output}
{command} → {expected output}

---
```

#### Feature Reference Table

At the end of the document, a quick-reference table:

| Chapter | Feature | Tier | Effort | Depends on |
|---------|---------|------|--------|-----------|
| 1 | {name} | Core | Medium | none |
| 2 | {name} | Core | High | Chapter 1 |
| 3 | {name} | Supporting | Low | Chapter 1 |

#### Blockers

Every `UNKNOWN` that could not be resolved:

| Chapter | Step | What is unknown | How to investigate |
|---------|------|-----------------|-------------------|
| {N} | {step name} | {what is unclear} | {specific question or check} |

If none: *"None — all features were fully understood."*

---

### Ordering

Sort chapters by dependency: no chapter appears before its prerequisites.

```
Chapter 1 — Core Feature A (no deps)
Chapter 2 — Core Feature B (depends on Ch. 1)
Chapter 3 — Supporting Feature C (depends on Ch. 1)
Chapter 4 — Core Feature D (depends on Ch. 2)
Chapter 5 — Minor Feature E (depends on Ch. 4)
```

---

## Phase 5 — Test Coverage

Tests are woven into each chapter in `docs/rebuild-tutorial.md` (see the **Tests** sub-section in each chapter template above). No separate document is required.

If a consolidated view is needed, produce `docs/tests/coverage-plan.md`:

```
# Test Coverage Plan

## Test Harness
{framework, install command, run-all command, run-one command, file location convention}

## Per-Feature Coverage

| Chapter | Feature | Tier | Effort | Unit Tests | Integration Tests | E2E Tests |
|---------|---------|------|--------|-----------|------------------|-----------|
| ...     | ...     | ...  | ...    | ...       | ...              | ...       |

## Risk Areas
Chapters that need integration tests, not just unit tests:
{High-effort chapters, chapters with concurrency, external calls, or complex state}
```
