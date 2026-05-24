# Skills Repository

Collection of OpenCode agent skill definitions.

## Structure

Each skill is a self-contained directory: `skills/<category>/<skill-name>/SKILL.md`

- `SKILL.md` is the entrypoint and sole file for each skill
- Categories are top-level groupings under `skills/` (e.g., `engineering`)

## Conventions

- No build step, test suite, linter, or package manager — this repo is markdown-only
- Skill files are standalone; they do not import or reference each other
- Adding a new skill means creating `skills/<category>/<name>/SKILL.md`
