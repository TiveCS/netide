# AGENTS.md

Instructions for AI coding agents working in this repository.
This is the single source of truth — `CLAUDE.md` only imports this file.

## Project

_TBD — project overview, stack, and conventions will be added here._

## Skills

Agent skills live in `.agents/skills/` (symlinked into `.claude/skills/` for Claude Code)
and are pinned in `skills-lock.json`. Manage them with the `skills` CLI:

- `npx skills list` — list installed skills
- `npx skills update` — update to latest versions
- `npx skills experimental_install` — restore from `skills-lock.json`
