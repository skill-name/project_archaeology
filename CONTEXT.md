# Project Archaeology Skill

A prompt/instruction skill for AI coding assistants. When loaded and pointed at a target codebase, it produces documentation sufficient to rebuild that project from scratch.

## Language

**Target codebase**:
A software project (usually vibecoded) that the skill analyzes.
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
A classification of features by criticality. Core features get full step-by-step treatment in the rebuild plan. Supporting features get outline treatment. Minor features are listed with a brief description. Every feature is listed regardless of tier.
_Avoid_: Priority, severity, category
