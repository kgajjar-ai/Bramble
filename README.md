# Bramble

When is it worth asking a specialist analyst to investigate a data question? Bramble is a planned pilot that compares two ways to get an answer from the AI assistant you already use. The assistant can investigate directly or hand the question to a specialist. Both get read-only access to the same records and the same work allowance.

## The pilot

Development uses three disclosed PostgreSQL samples, one per tier. After the build, an independent evaluator will compare both approaches on nine fresh held-out cases across three domains and three tiers. It will track which answers can be verified, how long they take, and how much each approach uses. Git holds the dataset documentation. The development machine holds the fixture tools and databases, and the evaluator keeps the blind material outside the checkout.

## Project status

Bramble is at the requirements stage. Development uses separate sample data. The independent evaluator shares the database type (relational) and expected scale with the developer, and selects the evaluation datasets privately.

- [Product requirements](PRD.md)
- [Local pilot scope](docs/specs/local-pilot.md)
- [Architecture draft](docs/architecture.md)
- [Sample databases, worked examples and local handoff](docs/sample-databases.md)
- [Business goals and success criteria](BRD.md)
- [Contributor guide](AGENTS.md)
