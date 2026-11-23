# Repository Guidelines

## Project structure

Bramble is at the requirements stage. The first working version will use one public dataset. The evaluation will cover a few public datasets chosen for the different questions they support.

- Read [BRD.md](BRD.md) when changing the pilot audience, business objectives, or criteria for deciding whether delegated analysis earns its cost.
- Read [PRD.md](PRD.md) when changing answer requirements, evaluation cases, review panel behavior, or release acceptance checks.

The local pilot compares direct investigation with delegation to a specialist analyst. Remote client access belongs to the hosted milestone.

## Development and review commands

Review changes in a Markdown preview. The checkout has no application, build scripts, or automated test suite. From the repository root, use these commands to locate related requirements before editing.

- `rg '^#{1,3} ' BRD.md PRD.md` lists document sections.
- `rg -in 'dataset|pilot' README.md BRD.md PRD.md` finds related statements about the planned comparison.

## Writing conventions

Follow the existing sentence-case headings and use backticks for tool names. Use tables for fixture rows and numbered lists for ordered acceptance checks. When defining a metric, identify the counted unit, timestamp, and treatment of missing records so a reviewer can reproduce the answer.

## Validating changes

Use development-only examples for implementation tests and panel previews. Keep the independent evaluator’s databases, cases, and scoring materials outside the development workspace and build pipeline.

## Commits and pull requests

Use a concise, descriptive commit subject such as `Clarify pilot dataset requirements`. In the pull request, identify the affected requirement, explain the decision, and include any revised expected answers or acceptance checks.
