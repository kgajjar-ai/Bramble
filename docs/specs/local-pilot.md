# Local pilot scope

This spec defines the first release of Bramble. It adds detail to the [product requirements](../../PRD.md), the [business requirements](../../BRD.md), and the [architecture](../architecture.md).

## Scope

The local pilot runs on one machine for one user. The user asks questions from Claude Code or Codex.

| In the local pilot | Outside the local pilot |
| --- | --- |
| Direct route and delegated route | Hosted access and sign-in |
| Scored trials in Claude Code | Claude and ChatGPT apps |
| One known-answer check in Codex | Scored trials in Codex |
| Panel with Evaluations and Requests, read only | Starting a run from the panel |
| PostgreSQL for analysis and for Bramble's own records | Other database engines |

The pilot ends with a table of each case. The table shows how often each route passed, how long it took, and how much it used. The owner reads the table and judges where the specialist is worth its cost. No fixed threshold decides this.

## Terms

| Term | Meaning |
| --- | --- |
| Request | One question on one route, with all the queries that serve it. |
| Trial | One request that the trial script runs for scoring. |
| Case | One question, one database, and one answer key. |
| Run | All the trials in one comparison. |
| Allowance | The limits that Bramble applies to one request. |
| Analysis database | The database that holds the records under investigation. |
| Application database | The database that holds Bramble's own records. |

## Connection and tools

Bramble serves the Model Context Protocol (MCP) over HTTP at `127.0.0.1`. It exposes three tools. The names below describe their work.

| Tool | Input | Output |
| --- | --- | --- |
| `inspect` | None | Request number, database identifier, tables, columns, and definitions |
| `query` | Request number, database identifier, one SQL statement | Rows, and a mark on a result that is cut off |
| `delegate` | One self-contained question | An answer, a clarification, or a failure reason |

Definitions are PostgreSQL comments on tables and columns. `inspect` returns the same text to both routes. Every analysis database carries its definitions as comments.

If the request number is unknown or the database identifier is not the active one, a `query` call stops before execution. The reply tells the caller to inspect again.

`delegate` is one call that waits for the result and sends progress. Claude Code and Codex need a tool timeout that is longer than the time limit of a request. If either assistant cannot hold the call open, one tool starts the work and a second tool reads the result.

## Read-only access

Both routes use the same access code.

1. The database account has read-only grants on the permitted tables.
2. Each query runs in a read-only transaction.
3. Each call carries one `SELECT` or `WITH` statement.
4. Each query has a time limit.
5. Each result has a row limit and a size limit. Bramble marks a result that reaches a limit as cut off.

The account is the main protection. The other layers limit damage and cost.

## Allowance

Both routes get the same allowance for each request.

| Limit | Counting rule |
| --- | --- |
| Queries | Every `query` execution counts, including a failed one. `inspect` does not count. All specialist agents share one count. |
| Rows | Rows returned to the caller or to an agent, added over the request. |
| Time | From the first tool call of the request. In a trial, the script also stops the session at this limit. |

Bramble measures tokens but does not limit them. The specialist reserves part of its allowance for the reviewer.

## Delegated route

The specialist is three agents in a fixed order. Pydantic AI runs them.

1. The interpreter reads the definitions. It states what is counted, the population, and the period. If more than one meaning changes the result, it returns a clarification.
2. The analyst calculates the result with queries.
3. The reviewer calculates the result again with its own query.
4. If the two results agree, Bramble returns the answer.
5. If they disagree, the analyst gets one more attempt and the reviewer compares again.
6. If they still disagree, Bramble returns the outcome `reviewers disagreed` with both results.

All three agents use the same model as the direct route. The model version is the same for every trial in a run. The specialist sees only the data that the direct route sees.

## Answer form

Both routes return the same form.

| Field | Content |
| --- | --- |
| Outcome | One value from the outcome list |
| Result | One or more values |
| Unit | The unit of each value |
| Population | Which records were counted |
| Period | The time period that applies |
| Query | One statement that reproduces the result |
| Open points | What the evidence does not settle, or the clarification question |

In a trial, the script saves this form from the assistant's final message. In everyday direct use, Bramble does not see the final message, so the answer stays `unknown`.

## Outcomes

| Outcome | Meaning |
| --- | --- |
| `answered` | The investigation completed. A count of zero is an answer. |
| `partial` | The result covers only part of the question and says which part. |
| `clarification` | More than one meaning changes the result. |
| `denied` | The records are outside the permitted access. |
| `timed out` | The time limit ended the request. |
| `allowance used` | The query limit or the row limit ended the request. |
| `reviewers disagreed` | The analyst and the reviewer did not reach the same result. |
| `database changed` | The active database is not the one that the caller inspected. |
| `system error` | Bramble, the model, or the database failed. |
| `unknown` | Bramble did not see the end of the request. |

An everyday direct request becomes `unknown` after 15 minutes without a call. A failed request counts as one failure, whatever the number of steps.

## Records

| Kind | What Bramble stores |
| --- | --- |
| Everyday request | Request number, route, database identifier, start time, duration, steps, observed usage, and outcome |
| Trial | Everything above, plus the question, queries, results, and answer form |

Everyday records hold no question text, SQL, or record contents. Everyday requests and trials stay distinguishable in every view. An unknown value is stored as unknown and never as zero.

## Trials

The trial script runs Claude Code in a fresh session for each trial.

1. The script opens a trial in Bramble. Bramble returns a connection address with a fixed request number.
2. The address exposes only the tools of the route. Direct trials get `inspect` and `query`. Delegated trials get `delegate`.
3. The session has no other tools, no web access, and no file access.
4. Every call on the address counts against the one request, so a second `inspect` cannot reset the allowance.
5. The script saves the answer form, elapsed time, and tokens.

Each case runs three times on each route. Nine cases make 54 trials. Both routes use one API key with a spending cap. The key is stored outside the repository.

| Measure | Rule |
| --- | --- |
| Elapsed time | From session start to the final message, as the script measures it. |
| Usage | Input, cached input, and output tokens of the session. A delegated trial adds the tokens of all specialist agents. |

## Scoring

The evaluator scores outside Bramble.

1. If every value matches the answer key and the supplied query reproduces the result on the same database, the trial passes. The key states the rounding for values that are not whole numbers.
2. Unit, population, period, and open points get separate scores. They do not change the pass.
3. A clarification ends a trial. If the key marks the question as ambiguous, the clarification passes. Otherwise it does not pass. An answer to an ambiguous question without clarification does not pass.
4. If at least two of its three trials pass, a route succeeds on a case. The table also shows the number of passes.
5. An unfinished or unscored trial is listed. It is not a pass or a failure.

## Evaluator separation

An AI agent under a separate operating-system account prepares and scores the held-out cases. A person makes sure that its answer keys are correct. If that person is the developer, this happens after all trials are recorded.

1. The held-out databases, questions, and keys stay under the evaluator account. The developer account and its AI assistants have no read access to them.
2. The build is frozen before a held-out trial runs.
3. If rule 1 or rule 2 is broken at any point, the results carry the label `not blind`.

## Saved run file

The trial script writes one JSON file for each run, and the evaluator adds the scores. A command imports the file into the application database.

The file records the build commit, model version, allowance, scoring rules version, database identifiers, and a fingerprint of each database's contents. It lists every planned trial with its status, including unfinished trials.

The import rejects a file with a missing field, a duplicate trial, or totals that do not match its trials. A rejected file changes nothing.

## Panel

The panel is React with Vite, and the FastAPI backend serves the built files. It is read only. Opening a view does not repeat any work.

- Evaluations compares two runs, finds changed outcomes, and shows both answers with evidence, time, and usage. It flags a difference in records, rules, allowance, or model between the runs.
- Requests finds a request by number, or filters by time, route, and outcome. It shows request and failure counts, and which step stopped.

The median wait covers finished requests with a known duration and shows its sample size. An empty period shows zero requests.

## Tests

Automated tests use a small PostgreSQL database that the test builds, with faked model replies. They need no sample kit and no API key. Negative cases come first:

- A write statement.
- Two statements in one call.
- A wrong database identifier.
- A result at the exact limit.
- A failure part way.
- An unreadable answer.

Known-answer runs use the three [development samples](../sample-databases.md) and a real model. A person starts them. Panel tests run in Chromium, Firefox, and WebKit at phone, tablet, and desktop sizes.

## Build order and acceptance

1. Direct route. From Claude Code, the Small question returns 6,770 runs with a query that reproduces it. Codex returns the same result. A write statement fails.
2. Trial script. Each development sample runs three times on the direct route. The file holds answers, time, and tokens. The session shows no tools outside its route.
3. Delegated route. All three samples run on both routes with the same allowance. `delegate` stays open to its end in Claude Code and Codex.
4. Requests view. A request that timed out is found by number, shows the step that stopped, and counts as one failure.
5. Evaluations view. Two runs with one changed score open side by side. The panel totals match the saved file, and an interrupted run shows how much completed.

## Values set during the build

| Value | Set when |
| --- | --- |
| Query, row, and time limits, and the reviewer's share | After direct route runs on the Enterprise sample, before the first delegated comparison |
| Model version | Before the first scored trial |

Both stay fixed for a whole run.
