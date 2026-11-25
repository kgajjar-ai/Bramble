# Bramble architecture draft

## From question to evidence

The user's usual AI assistant owns the conversation. In the local pilot it is Claude Code or Codex. It connects to Bramble over local HTTP with the Model Context Protocol (MCP), which exposes three tools. The [local pilot scope](specs/local-pilot.md) holds the detailed rules.

```text
Assistant → MCP → inspect schema or run query ─┐
                → delegate → Bramble analyst ──┴→ shared read-only access → active RDBMS

Browser → React panel → review API → Bramble's own records
```

**Single-user v1.** One person asks questions, reviews runs, and administers the local installation. Two databases serve different purposes.

| Database | Access | Purpose |
| --- | --- | --- |
| Active analysis database | Both routes use a database account with read-only grants on the permitted tables. | Supplies the records being investigated. |
| Bramble application database | The backend reads and writes its own records. | Preserves request history and saved evaluation results for review across restarts. |

**Changing the active database.** The user selects one connection in local configuration and restarts Bramble to activate it. Schema inspection returns an identifier for the active database. Each subsequent query supplies that identifier. If it no longer matches, Bramble stops before executing the query and asks the caller to inspect the active database again. Both routes record the database identifier with each query. A later feature will let users register hosted databases by supplying their connection details.

**Direct investigation.** The assistant chooses what to inspect and query. Bramble enforces the caller's access, query limits, and timeouts. It identifies incomplete results so the caller can interpret them correctly.

**Delegated investigation.** Bramble owns the investigation and uses the same data access and limits. It returns an answer with reproducible evidence, a focused clarification, or a failure reason. On clarification, the assistant asks the user and submits a fresh, self-contained question. The specialist is three agents in a fixed order: an interpreter, an analyst, and a reviewer.

**Record ownership.** Bramble writes request identifiers, timings, outcomes, and observed usage to its own storage. The review API distinguishes Bramble's observations from information supplied by a client or evaluator. For example, a direct query can have a known duration while the assistant's final answer and total model usage remain unknown.

## Local deployment

The single-user development setup runs three containers:

```text
Browser and AI assistant → Bramble backend (React panel, review API, MCP tools)
                              ├─ read only  → Analysis database server
                              └─ read/write → Bramble application database
```

The analysis server can host the three development samples using the same engine. Local configuration selects one at a time. The application database has its own persistent storage so replacing the samples preserves request history and saved results. The backend serves the built React files to the browser.

## Walk through one review

Open a saved run in **Evaluations** and select a question. The browser asks the backend for both answers, their evidence, the checked answer, elapsed time, and usage. React displays them side by side. Opening the evidence reads the saved calculation. A run recorded tomorrow appears through the same API, using the same frontend build.

## Evaluation during development

Use the [three development samples](sample-databases.md), one each at Small, Medium and Enterprise scale, while building Bramble. As each route becomes usable, ask it a question with a known answer and check its result against the sample records. Rerun those questions after changes to see whether earlier answers still hold. These sample runs can also supply realistic results for testing the review panel.

## Independent evaluation

The independent evaluator holds nine fresh relational databases across three domains and three tiers. Development uses only the disclosed samples.

The evaluator controls access to the selected databases, schemas, questions, scoring rules, and answer keys outside the development workspace and build pipeline. The evaluator runs the finished build through its normal interface, gives both routes equivalent read-only access, and scores their recorded answers externally. Either side can change independently as long as the interface stays compatible.

The panel reads evaluator-provided results at runtime. Keep held-out results in the evaluator’s review environment. A fresh evaluation can run against the same build.

## Where the work belongs

| Path | Responsibility |
| --- | --- |
| `backend/` (proposed) | Run the two analysis routes, enforce shared read limits, record outcomes, and serve the review API. Keep tests of those behaviors here. |
| `frontend/` (proposed) | Implement Evaluations and Requests, with tests for what the reviewer sees and can do. |
| [Sample database guide](sample-databases.md) | Documents the disclosed development samples and where the operator keeps the local kit. |
| `docs/specs/` | Keep a reviewed spec for each feature alongside the architecture note in `docs/`. |

## What the reference implementation verifies

In the supplied Parkify archive, `frontend/package.json` runs `vite build`, and `frontend/vite.config.ts` forwards development requests under `/api` to the backend. In production, `docker/Dockerfile` builds the frontend in a Node stage and copies its `dist/` into the Python runtime image. In `backend/app/main.py`, FastAPI registers the API routes before mounting the built frontend at the root. This gives developers quick feedback while editing React and gives users one address when the application is running.

## Technology choices

- **Frontend tooling.** React with Vite, built to static files that the FastAPI backend serves.
- **First panel interaction.** The panel opens completed comparisons and is read only, consistent with the [product requirements](../PRD.md).
- **Database engines.** PostgreSQL is the analysis engine and the application database. The project owner runs migrations unless explicitly delegated.
- **Specialist library.** Pydantic AI runs the specialist's agents.
