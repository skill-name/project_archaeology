---
name: project-archaeology
description: Reverse engineer any codebase and produce documentation sufficient to rebuild it from scratch without AI help. Outputs a forensic analysis (what the code does) and a rebuild plan (how to build it properly, per feature, in dependency order).
---

## Goal

You are a Senior Software Architect, QA Engineer, Technical Writer, and Reverse Engineer.

Your task is to completely understand an existing target codebase and produce two documents:

1. **Forensic Analysis** — what the code does, extracted from reading the source
2. **Rebuild Plan** — how to build it properly from scratch, per feature, in topological order

The rebuild plan is *aspirational*: it designs a clean implementation for the same behavior, not a transcription of the original code.

Derive behavior from code. Derive design from principles.

## Outputs

| File | Phase | Content |
|------|-------|---------|
| `docs/forensic-analysis.md` | 2 | Architecture, data model, dependencies, behavioral observations |
| `docs/rebuild-plan.md` | 4 | Per-feature what-it-does + how-to-rebuild-it, topologically sorted |

## Quality Bar

- Every Core-tier feature section in the rebuild plan must include a Mermaid sequence or flow diagram
- Every feature must list its dependencies by name (or "none")
- Checkpoints must include a verifiable command the developer can run
- The rebuild plan must be buildable from an empty repo — no "see the original code" references
- Every feature in the target codebase must be listed in the rebuild plan (Core / Supporting / Minor)
- When uncertain about a forensic finding, mark it as `UNKNOWN` — do not hallucinate

## Rules

- Cite source files for forensic claims only. The rebuild plan is aspirational — do not cite it.
- The rebuild plan must be self-contained. A developer needs nothing except this document and their own tools.
- Do not assume implementation details when describing what the code does.
- Be exhaustive in feature discovery. Leave nothing out.
- Generate Mermaid diagrams for data flow, architecture, and state machines.
- Prefer evidence from code over inference.
- When uncertain, mark as `UNKNOWN`.

---

## Phase 1: Project Inventory

Read the entire repository structure. Produce nothing yet — this phase builds the context for all subsequent phases.

Understand:

- **Project purpose**: What does it do? What problem does it solve?
- **Primary users**: Who uses it?
- **Technology stack**: Language, framework, database, runtime, major libraries
- **File tree**: Every folder's purpose, key files
- **Entry points**: Where does execution start? (main files, routes, CLIs)
- **Build & run commands**: How to install, build, test, and run
- **Dependencies**: All external packages/libraries with versions

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

Code audit findings (dead code, security concerns, tight coupling) go here as a sub-section.

---

## Phase 3: Feature Extraction

Identify every feature in the target codebase. Do not skip anything.

For each feature, extract:

```
## Feature: {name}

**Purpose:** 1-2 sentences

**Triggers:** What starts this? User action? Event? Cron?

**Inputs / Outputs:** What goes in, what comes out

**Primary files:** File paths in the target codebase

**Edge cases & failure scenarios:** What the code handles or fails to handle
```

After extracting all features, classify each into a **tier**:

| Tier | Meaning | Rebuild plan treatment |
|------|---------|----------------------|
| **Core** | Essential to the app's purpose | Full step-by-step implementation |
| **Supporting** | Important but not defining | Outline implementation |
| **Minor** | Nice-to-have or peripheral | Listed with brief description |

Every feature is documented. Nothing is left out.

---

## Phase 4: Rebuild Plan

Produce: `docs/rebuild-plan.md`

This is the primary output. A developer should be able to rebuild the entire project from scratch using only this document.

### Template

```markdown
# Rebuild Plan: {Project Name}

## Overview

{1-2 sentences describing what the rebuild produces}

## Schema Overview

{if the project uses a database}
Overall schema: all tables/collections, their fields, constraints, and relationships.
(Mermaid ER diagram)
{if no database, skip this section entirely}

## Prerequisites

{language runtime, package manager, database server, etc.}

## Build Order

{ordered list of features, topologically sorted — no feature appears before its dependencies}

---

## Feature 1: {name}

**Tier:** Core / Supporting / Minor
**Dependencies:** Feature X, Feature Y (or "none")

### What It Does

2-3 sentences derived from the forensic analysis.

### How to Rebuild It

{For Core features: step-by-step implementation, architecture notes, code structure}
{For Supporting features: outline of what needs to happen}
{For Minor features: brief description of what to build}

Include a Mermaid diagram (sequence, flow, or component diagram) for every Core feature.

### Checkpoint

A verifiable command the developer can run to confirm this feature works:
```
curl http://localhost:3000/...
```
or
```
npm test -- --grep "feature name"
```

### Tests

What to test and how. Unit tests for business logic, integration tests for boundaries.

---

## Feature 2: {name}
...
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

### Feature dependencies reference

End the document with a flat list of every feature, its tier, and its dependencies — a quick-reference table the developer can scan.

---

## Phase 5: Test Coverage

No separate document. Instead, ensure every feature in the rebuild plan includes a **Tests** sub-section (see template above).

If the developer needs a consolidated view, generate `tests/coverage-plan.md` as a final step:

```markdown
# Test Coverage Plan

## Per-Feature Coverage

| Feature | Tier | Unit Tests | Integration Tests | E2E Tests |
|---------|------|-----------|------------------|-----------|
| Auth | Core | Login validation, password hashing | Login endpoint, token refresh | Sign-up → login → protected route |
| ... | ... | ... | ... | ... |

## Risk Areas

{features with complex state, concurrency, external integrations, etc.}
```
