# Bramble product requirements

## Asking a question

From my usual AI assistant, I can ask a question about data I’ve made available. I can have the assistant investigate directly or hand the same question to a specialist analyst. Both approaches let me see what the records mean, which records were counted, and how to reproduce the answer.

If a question has more than one plausible interpretation and the choice affects the result, Bramble should ask for clarification.

## Pilot data

Develop against three disclosed sample relational databases, one per tier across three domains. The independent evaluator privately selects public source records and prepares nine fresh cases covering three domains at all three tiers. During a trial, each route can inspect the permitted records and their definitions.

## What an answer must show

- State the result, unit, population, and applicable time period. Show how I can repeat the calculation using the same records. If I ask what caused a change, identify what evidence supports a cause and what remains open.
- Report zero when a completed count finds no matches. If access was denied, time ran out, or the work allowance was exhausted, explain why the answer is unavailable. Label partial results.
- Keep both approaches within the same read-only access. Limit how long an investigation can run and how much data it can return. The specialist can use only the data available to me.

## Comparing the two approaches

An independent evaluator runs the finished application against the nine held-out cases, three times on each route. Both routes receive the same question, records, and total work allowance. The evaluator checks the answers and supplies the results for private review. Development and build checks use separate examples. The [local pilot scope](docs/specs/local-pilot.md) defines the allowance, the trials, and a passing answer.

## Reviewing results and requests

The local release includes a private, read-only panel with two views. Opening either view shows the recorded work without repeating it.

- **Evaluations.** Compare two runs and find a question whose outcome changed. Inspect the expected answer, actual answer, evidence, and cost for each approach. Flag changes to records, rules, or allowances that affect the comparison. Totals must agree with the saved report and identify unfinished or unscored trials.
- **Requests.** Find a request by its reference number or filter by time, approach, and outcome. For example, I can locate a request that ran out of time, see which step stopped, and verify that it counts as one failure even if the specialist took several steps. Show request and failure counts and the median wait with its sample size. Keep pilot trials distinguishable from everyday activity. Mark an answer as correct only when someone has verified it or it matches a checked answer.

## Release checks

For the local pilot, I can reproduce a checked answer, review the comparison, explain a changed score, and investigate a failed request. An empty review period shows zero requests. An interrupted comparison shows how much was completed.

For the hosted milestone, I can repeat a read-only question from Claude and an OpenAI client using the same sample records. Both approaches must deny access to data outside my permissions. Access to the review panel requires separate permission. Everyday request history shows timing, usage, and outcomes while keeping question text, record contents, and sign-in details private.
