# Project Archaeology Skill

A reverse-engineering skill for AI coding assistants. Analyzes any codebase and produces two documents — a forensic analysis of what the code does, and a rebuild plan for building it properly from scratch without AI help.

## What It Produces

| Document | File | Content |
|----------|------|---------|
| Forensic Analysis | `docs/forensic-analysis.md` | Architecture, data model, dependencies, behavioral observations |
| Rebuild Plan | `docs/rebuild-plan.md` | Per-feature what-it-does + how-to-rebuild-it, topologically sorted, with checkpoints |

## Installation

### Option A: Manual

```bash
mkdir -p ~/.agents/skills/project-archaeology
cp SKILL.md ~/.agents/skills/project-archaeology/
```

### Option B: In-repo reference

Keep `SKILL.md` in your project root. Reference it with `@SKILL.md` or load via your assistant's skill command (e.g. `/skill project-archaeology`).

## How to Use

1. Place `SKILL.md` where your AI assistant can find it.
2. Reference the skill at the start of your session: `@SKILL.md`
3. The assistant executes 5 phases:

   - **Phase 1** — Project Inventory (file tree, tech stack, entry points)
   - **Phase 2** — Forensic Analysis → `docs/forensic-analysis.md`
   - **Phase 3** — Feature Extraction (every feature classified as Core / Supporting / Minor)
   - **Phase 4** — Rebuild Plan → `docs/rebuild-plan.md` (per feature, topologically sorted, with checkpoints)
   - **Phase 5** — Test Coverage (embedded per-feature, optional aggregated `tests/coverage-plan.md`)

## Requirements

- An AI coding assistant that supports skill loading
- Read access to the target codebase
- Mermaid rendering support (for diagrams in the rebuild plan)

## Quality Bar

- Every Core feature includes a Mermaid diagram
- Every feature lists its dependencies
- Checkpoints include a verifiable command
- Rebuild plan is buildable from an empty repo — no "see the original code" references
- Every feature in the target codebase is listed
- Uncertain forensic findings are marked `UNKNOWN`
