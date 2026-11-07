# AGENTS.md

Instructions for AI coding agents working in this repository.
This is the single source of truth: `CLAUDE.md` only imports this file.
Where an installed skill and this file disagree, this file wins.

## Project

_TBD: the domain, stack, and conventions are decided in a separate session._

## Focus: one ticket at a time

- Work on one ticket until it is **done**, then move on. Subagents serve only the active ticket.
- Within the active ticket, run up to 3 subagents at once when more hands finish it sooner.
- Why: fanning out across tickets risks hitting the session limit with nothing finished. A finished ticket beats three half-done ones.
- **Done** means all of these are true:
  - checks are green
  - every behaviour change is covered by tests
  - an Opus review passed
  - docs are synced to `main`
  - the code is pushed
- After a ticket is done, continue to the next one only when the user gave a queue. Otherwise stop and report.

## Subagent models

Match the model to the task:

- **Opus**: domain and business reasoning, architecture and infrastructure analysis, specs and ADRs, code review, security review, hard debugging, anything that must not be wrong.
- **Sonnet**: implementation, tests, refactoring, routine fixes.
- **Haiku**: searching and reading the codebase, fetching docs and web pages, mechanical edits, summaries.

Move up a tier when the task is hard, for example Opus implements a complex ticket. When unsure, go up.

## Who decides

The user is the architect with a helicopter view. They do not follow the implementation.

- **Domain and business rules: the user decides.**
  - Ask every time. Never assume.
  - Present options neutrally, with no recommendation, unless the user asks for one. They often pair the request with the `council` skill.
  - This overrides the grilling skill's "recommended answer" for domain questions.
  - Process and tooling questions may carry a recommendation.
- **High-level technical choices: decided together.**
  - This covers infrastructure, the database engine, tech stack, frameworks, major libraries, and public API design that affects users.
  - Ask, and recommend with full trade-offs: benefit, cost, downside, catch.
- **Implementation: the agent decides.**
  - This covers database schema, internal APIs, minor libraries, and code structure.
  - Decide with long-term care. Escalating these questions costs the user context they don't have.
- **Workarounds: discuss with the user first.**
  - Research the real fix first.
  - Weigh alternatives, such as another library. Building our own is the last resort.
  - An approved workaround is always documented: a comment linking the GitHub issue that tracks the real fix.

## Code

- Optimise for long-term maintainability over speed of delivery. Follow the language's and framework's best practices and the design patterns that fit.
- Clean Code (Robert C. Martin) is the default. Where it clashes with the ecosystem's idioms, the idioms win.
- Code is self-explanatory: names and structure carry the meaning.
  - Comments explain only *why*:
    - an approved workaround, with its issue link
    - a non-obvious external constraint
    - public API doc comments where the language expects them
  - When code needs a *what* comment, rename or extract instead.
- Express options as enums or declared types, never boolean parameters.
- Every behaviour change ships with tests. Business logic is written test-first (`tdd` skill).
- Stack-specific standards will live in `docs/coding-standards.md` once the stack is chosen.

## Git

- **Docs go straight to `main`**, so they never drift.
  - Docs are: `AGENTS.md`, `CLAUDE.md`, `README.md`, `GLOSSARY.md`, everything under `docs/`, `.agents/skills/`, and `skills-lock.json`.
  - Commit them as `docs:` and push directly to `main`, with no PR.
  - Sync them as the work goes, at the latest when a ticket is done.
- Docs and code never share a commit.
- **Code goes through a ticket branch** named `<issue-number>-<short-slug>`, cut from `main`.
  - Rebase onto the latest `main` before opening the PR, then push with `--force-with-lease`.
- Commit messages follow Conventional Commits (`feat:`, `fix:`, `docs:`, `refactor:`, …).
- **Merging:**
  - The agent merges a code PR once the ticket is done.
  - A PR that touches user-facing public API or a high-level technical choice waits for the user.

## Skills

Skills live in `.agents/skills/` (symlinked into `.claude/skills/`) and are pinned in `skills-lock.json`.

- Keep skill files untouched, so `npx skills update` stays a clean upgrade. Project-specific overrides belong in this file.
- `npx skills list`: list the installed skills
- `npx skills update`: update to the latest versions
- `npx skills experimental_install`: restore from `skills-lock.json`

## Agent skills

### Issue tracker

GitHub Issues on `TiveCS/netide`; where the `gh` CLI is unavailable, use the equivalent GitHub MCP tools. See `docs/agents/issue-tracker.md`.

### Triage labels

The five default labels: `needs-triage`, `needs-info`, `ready-for-agent`, `ready-for-human`, `wontfix`. See `docs/agents/triage-labels.md`.

### Domain docs

Single-context: one `GLOSSARY.md` and `docs/adr/` at the repo root, created lazily. See `docs/agents/domain.md`.
