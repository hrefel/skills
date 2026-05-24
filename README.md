# OpenCode Skills

A collection of reusable skill definitions for [OpenCode](https://opencode.ai) agents. Each skill provides specialized instructions and workflows that an AI coding assistant can follow to perform specific tasks consistently and correctly.

## What Are Skills?

Skills are markdown files (`SKILL.md`) that teach an OpenCode agent how to approach a particular domain or workflow. When you activate a skill, the agent loads its instructions and follows them step-by-step — no manual prompting required.

## Available Skills

### Feature Lifecycle

**Location**: [`skills/feature-lifecycle/SKILL.md`](skills/feature-lifecycle/SKILL.md)

Build a complete feature from requirements to working code. Uses a 9-phase pipeline that covers BDD spec generation, test-first implementation across clean architecture layers, DI wiring, and optional UI composition. Stack-agnostic and language-agnostic.

[Read full documentation](skills/feature-lifecycle/README.md)

### BDD (Behavior-Driven Development)

**Location**: [`skills/engineering/bdd/SKILL.md`](skills/engineering/bdd/SKILL.md)

Generate behavioral specifications and drive test-first implementation for individual units. Produces structured specs covering intent, invariants, observable behaviors, edge cases, and failure modes — then writes failing tests and minimal implementation to satisfy them.

[Read full documentation](skills/engineering/bdd/README.md)

## Repository Structure

```
skills/
├── feature-lifecycle/          # Full feature lifecycle pipeline
│   ├── SKILL.md               # Skill definition (entrypoint)
│   └── README.md              # Usage documentation
└── engineering/
    └── bdd/                   # BDD / TDD for individual units
        ├── SKILL.md           # Skill definition (entrypoint)
        └── README.md          # Usage documentation
```

## Adding a New Skill

1. Create a directory: `skills/<category>/<skill-name>/`
2. Add a `SKILL.md` file with the skill definition
3. Optionally add a `README.md` with user-facing documentation

### SKILL.md Format

Every `SKILL.md` must start with a frontmatter block:

```yaml
---
name: skill-name
description: Short description of what the skill does.
---
```

Followed by the skill's instructions in markdown.

## Conventions

- **Markdown-only** — no build step, test suite, linter, or package manager
- **Standalone** — skill files do not import or reference each other
- **Self-contained** — each skill directory contains everything needed for its workflow
