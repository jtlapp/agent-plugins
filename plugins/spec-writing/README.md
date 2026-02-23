# spec-writing

A Claude Code skill that provides guidance for planning and writing changes to software specification documents, striving for precision and the progressive development of concepts.

## What It Does

This skill enforces discipline when working on multi-document specifications:

- **Cross-document consistency** — Changes are traced across all affected documents
- **Terminology discipline** — Terms are defined before use; synonyms and variant phrasings are prevented
- **Progressive explanation** — Concepts are introduced before they are referenced
- **Requirements compliance** — Changes are verified against requirements and constraints
- **Robustness analysis** — New behaviors are stress-tested against concurrency, partial failure, stale data, and other edge cases
- **Issues tracking** — Deferred decisions and open questions are captured rather than silently assumed

The skill uses a **SPECFILES.md** manifest to define the project's spec structure, document reading order, and project-specific checks.

## Installation

1. Copy `SKILL.md` into your project's Claude Code skills directory:

```
.claude/skills/spec-writing/SKILL.md
```

2. Create a `SPECFILES.md` manifest in your working directory (or run `/spec-writing init` to generate one interactively).

## Usage

The skill activates automatically when you enter plan mode for spec changes, or you can invoke it directly:

- `/spec-writing` — Use when planning or writing spec changes
- `/spec-writing init` — Create a new SPECFILES.md manifest and starter spec files

## SPECFILES.md

The manifest defines your spec structure. Minimal example:

```markdown
## Documents

Document order defines reading order, where earlier documents introduce terms and concepts used by later documents.

- `spec/overview.md`: System overview and core concepts.
- `spec/glossary.md`: Defines all terms used across the specification.
- `spec/model.md`: Subsystems, domain objects, database schemas.
- `spec/behavior.md`: Processes operating on the model.
- `spec/issues.md`: Outstanding questions and deferred decisions.
```

Optional sections for project-specific checks:

- **Universal Checks** — Robustness questions to ask for any new behavior
- **Conditional Checks** — Checks triggered by particular kinds of changes

See `SKILL.md` for the full specification and a detailed example.
