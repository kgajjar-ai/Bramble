# Bramble product requirements

## Asking a question

From my usual AI assistant, I can ask a question about data I’ve made available. I can have the assistant investigate directly or hand the same question to a specialist analyst. Both approaches let me see what the records mean, which records were counted, and how to reproduce the answer.

For example, if I ask “How many jobs were slow?”, Bramble should ask whether I mean time spent waiting to start or time spent running. If I ask “How many jobs waited over a minute before they started?”, it has enough information to count them.

## A worked example

The pilot uses synthetic records inspired by a past image-generation project. A **job** is one submitted request. An **attempt** is one try at completing it. A retry adds an attempt to the same job.

All three jobs below were submitted at the same time. Times shown are minutes and seconds since submission.

| Job | First attempt started | First attempt finished |
| --- | --- | --- |
| A | 1 min 10 sec | 1 min 40 sec |
| B | 10 sec | 1 min 40 sec |
| C | Has not started | — |

One job waited over a minute to start (A), and one completed attempt ran over a minute (B’s). Now give B a second attempt starting at 2 min and finishing at 3 min 30 sec. The job count stays at **one** because B’s first start is unchanged. The long-attempt count rises to **two**, both belonging to B.

Both approaches must use the same definitions. Queue wait ends at the first start. A job awaiting its first start has an unknown wait, and an unfinished attempt has an unknown completed duration. Exactly 60 seconds falls outside “over a minute.” For a date range, name the event being counted and use UTC, including the start of the period and excluding its end.

## What an answer must show

- State the result, unit, population, and applicable time period. Show how I can repeat the calculation using the same records. If the data shows a slowdown, identify what further evidence would establish its cause.
- Report zero when a completed count finds no matches. If access was denied, time ran out, or the work allowance was exhausted, explain why the answer is unavailable. Label partial results.
- Keep both approaches within the same read-only access. Limit how long an investigation can run and how much data it can return. The specialist can use only the data available to me.

## Comparing the two approaches

1. Set aside ten pairs of questions. For each pair, prepare a small change to the records and hand-check the expected answers before testing. Cover retries, time limits, missing records, and questions that call for clarification. Keep expected answers private during the comparison.
2. Ask each question in a fresh conversation, using both versions of the records and both approaches. This gives 80 trials. Use the same model settings and total work allowance, counting the specialist’s work as part of the handoff.
3. Check the value, unit, and population by repeating the calculation. Credit a clarification when it identifies the ambiguity recorded in advance. An approach passes a pair when it answers both questions acceptably on both versions of the records.
4. Report individual outcomes, how many pairs passed out of how many reviewed, elapsed time, and total usage. Show an example that explains a difference or a shared result. If a scoring mistake calls for a rerun, keep the original result and explain the correction.

## Reviewing results and requests

The local release includes a private, read-only panel with two views. Opening either view shows the recorded work without repeating it.

- **Evaluations.** Compare two runs and find a question whose outcome changed. Inspect the expected answer, actual answer, evidence, and cost for each approach. Flag changes to records, rules, or allowances that affect the comparison. Totals must agree with the saved report and identify unfinished or unscored trials.
- **Requests.** Find a request by its reference number or filter by time, approach, and outcome. For example, I can locate a request that ran out of time, see which step stopped, and verify that it counts as one failure even if the specialist took several steps. Show request and failure counts and the median wait with its sample size. Keep pilot trials distinguishable from everyday activity. Mark an answer as correct only when someone has verified it or it matches a checked answer.

## Release checks

For the local pilot, I can repeat the worked example, review the comparison, explain a changed score, and investigate a failed request. An empty review period shows zero requests. An interrupted comparison shows how much was completed.

For the hosted milestone, I can repeat a read-only question from Claude and an OpenAI client using the same sample records. Both approaches must deny access to data outside my permissions. Access to the review panel requires separate permission. Everyday request history shows timing, usage, and outcomes while keeping question text, record contents, and sign-in details private.
