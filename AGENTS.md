# Repository Guidelines

## Project structure

Bramble is currently a requirements-only repository. Its two root documents serve different review decisions.

- Read [BRD.md](BRD.md) when changing the pilot audience, business objectives, or criteria for deciding whether delegated analysis earns its cost.
- Read [PRD.md](PRD.md) when changing DuckDB metrics, MCP tool behavior, evaluation cases, operator panel behavior, or release acceptance checks.

The local pilot covers direct SQL and delegated analysis through `ask_analyst`. OAuth and remote client interoperability belong to the hosted milestone.

## Development and review commands

Review changes in a Markdown preview. The checkout has no application, build scripts, or automated test suite. From the repository root, use these commands to locate related requirements before editing.

- `rg '^#{1,3} ' BRD.md PRD.md` lists document sections.
- `rg -in 'queue wait|earliest attempt' BRD.md PRD.md` finds metric definitions and related examples to check together.

## Writing conventions

Follow the existing sentence-case headings and use backticks for tool names. Use tables for fixture rows and numbered lists for ordered acceptance checks. When defining a metric, identify the counted unit, timestamp, and treatment of missing records so a reviewer can reproduce the answer.

## Validating changes

For metric changes, walk through both snapshots in the PRD’s “First acceptance fixture.” Adding job B’s retry keeps the count of jobs waiting over a minute at one and raises the count of completed attempts running over a minute from one to two. Check each affected example against its own unit and threshold.

## Commits and pull requests

Git history is unavailable in this checkout. Use a concise, descriptive commit subject such as `Clarify queue wait for jobs without attempts`. In the pull request, identify the affected requirement, explain the decision, and include any revised fixture counts or acceptance checks.
