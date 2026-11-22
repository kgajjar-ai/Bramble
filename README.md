# Bramble

When is it worth asking a specialist analyst to investigate a data question? Bramble is a planned pilot that compares two ways to get an answer from the AI assistant you already use. The assistant can investigate directly or hand the question to a specialist. Both get read-only access to the same records and the same work allowance.

## The pilot

“How many jobs were slow?” sounds like a counting question, but a job that waited a minute to start tells a different story from one that took a minute to finish. The pilot uses synthetic records from an image-generation workflow to check whether each approach spots that ambiguity, asks for clarification, and gives an answer someone can verify.

The comparison covers ten pairs of questions. Each question is asked before and after a small change to the records. Hand-checked answers, elapsed time, and total usage will show where the specialist earns the extra cost.

## Project status

Bramble is at the requirements stage. Start with the worked example in the product requirements to see how one retry changes the expected answer.

- [Product requirements and worked example](PRD.md#a-worked-example)
- [Business goals and success criteria](BRD.md)
- [Contributor guide](AGENTS.md)
