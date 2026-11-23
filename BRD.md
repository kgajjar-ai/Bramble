# Bramble business requirements

## Purpose

I’m an AI engineer. I’m using Bramble to decide when it’s worth asking a specialist analyst to investigate a question. The starting point is the AI assistant I already use. If it can give me an answer I can verify on my own, involving a specialist adds time and cost. I want to see which questions justify that expense.

## The pilot decision

I’ll develop both approaches on three disclosed samples, one per tier. After the build, an independent evaluator will test them on nine fresh databases drawn from public records, covering three domains at all three tiers. I want to see whether the specialist’s value depends on the kind of analysis. For each case, both approaches will get the same question and records.

## What I need to decide

| Decision | Evidence I need |
| --- | --- |
| Can I act on this answer? | What was counted, over which period, and enough evidence to reproduce the count. |
| When is a specialist worth using? | Questions where only one approach succeeded, where both succeeded, and where both failed. Include waiting time and total usage for each. |
| Can I trust either approach with the data? | Both can inspect only the records I make available, and neither can change them. |
| What happened when a result changed? | One place to compare earlier and later answers or look up an individual request and see where it slowed down or failed. |

## Delivery and success

I’ll verify the expected answers by hand and compare both approaches across the independently selected datasets. I’ll review the results by dataset and question type, including ties and failures, to decide where the extra work is worthwhile.

Hosted access is a separate milestone. I want to repeat a question from Claude and an OpenAI client and verify that access limits hold for both approaches. Before extending the findings to another domain, I’d want someone who knows that work to supply a real question and judge whether the answer helps them make a decision. The repeatable pilot checks are in the [product requirements](PRD.md).
