# Project Archaeology Skill

A reverse-engineering skill for AI coding assistants. It systematically analyzes any codebase and generates comprehensive documentation, tests, and rebuild plans — enough to reconstruct the project from scratch without referencing the original code.

## What It Does

Given a codebase, this skill produces:

| Artifact | File | Purpose |
|---|---|---|
| Project Map | `docs/project-map.md` | File inventory, folder purposes, dependencies |
| Architecture | `docs/architecture.md` | System overview, component diagram, data flow |
| Features | `docs/features.md` | Feature specs with user stories and edge cases |
| API Docs | `docs/api.md` | Endpoint schemas with examples (OpenAPI-style) |
| Database Schema | `docs/database.md` | Tables, columns, relationships, ER diagrams |
| Domain Model | `docs/domain-model.md` | Entities, aggregates, services, business rules |
| Dependencies | `docs/dependencies.md` | Why each dep exists, risks, alternatives |
| Test Plan | `tests/coverage-plan.md` | Coverage gaps, risk areas, test targets |
| Rebuild Plan | `docs/rebuild-plan.md` | Educational-to-production build order |
| Code Audit | `docs/audit.md` | Dead code, security issues, maintainability |
| Design Rationale | `docs/why.md` | Why major decisions were made and tradeoffs |
| Reconstruction Guide | `docs/reconstruction.md` | Step-by-step rebuild from empty repo |

## Installation

### Option A: Manual (copy to agent skills directory)

```bash
mkdir -p ~/.agents/skills/project-archaeology
cp SKILL.md ~/.agents/skills/project-archaeology/
```

### Option B: Install via bunx skills

```bash
bunx skills add skill-name/project_archaeology
```

This fetches and registers the skill from a remote source into your local skills directory.

### Option C: In-repo reference

Keep `SKILL.md` in your project root. When using an AI coding assistant that supports skill loading, reference it with:

```
@SKILL.md
```

or use the skill loader command (tool-specific, e.g. `/skill project-archaeology`).

## How to Use

1. Place `SKILL.md` where your AI assistant can find it (see Installation).
2. Reference the skill at the start of your session:

   ```
   Load the project-archaeology skill.
   @SKILL.md
   ```

3. The assistant will execute all 12 phases (or a subset you request):

   - **Phase 1** — File inventory
   - **Phase 2** — Architecture analysis
   - **Phase 3** — Feature discovery
   - **Phase 4** — API documentation
   - **Phase 5** — Database schema
   - **Phase 6** — Domain model
   - **Phase 7** — Dependency analysis
   - **Phase 8** — Test generation
   - **Phase 9** — Rebuild breakdown
   - **Phase 10** — Code quality audit
   - **Phase 11** — Knowledge capture
   - **Phase 12** — Reconstruction challenge

## Requirements

- An AI coding assistant that supports skill loading (e.g. opencode with `.agents/skills/`)
- Access to the target codebase (any language, any framework)
- Read permission on all source files

## Output

All documentation is written to `docs/` and test plans to `tests/` in the target repository. Files are in Markdown with Mermaid diagrams where applicable.

## Principles

- No assumptions — every conclusion is derived from source code
- Be exhaustive, not summarized
- When uncertain, mark as `UNKNOWN`
- Always cite source files that support conclusions
