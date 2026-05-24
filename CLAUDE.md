# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

A collection of OpenCode agent skill definitions. Markdown-only — no build step, no tests, no package manager.

## Structure

```
skills/<category>/<skill-name>/SKILL.md
```

- `SKILL.md` is the entrypoint and sole file per skill
- Categories are top-level groupings under `skills/` (e.g., `engineering`)
- Skills are standalone — they do not reference each other

## Skill file format

Each `SKILL.md` must start with YAML frontmatter:

```yaml
---
name: <slug>
description: <one-line summary used by the agent to decide when to activate this skill>
---
```

The body is freeform markdown that tells the agent what to do when the skill is active. The description field is critical — it's what agents match against when deciding which skill applies.

## Adding a skill

Create `skills/<category>/<new-skill-name>/SKILL.md`. No registration or index update needed.
