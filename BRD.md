# Bramble business requirements

## Purpose

I’m an AI engineer. I’m using Bramble to decide when it’s worth asking a specialist analyst to investigate a question. The starting point is the AI assistant I already use. If it can give me an answer I can verify on my own, involving a specialist adds time and cost. I want to see which questions justify that expense.

## The pilot decision

An image-generation service I worked on provides a familiar example. Suppose someone says generation felt slower yesterday. Did jobs wait longer to start, take longer to finish once started, or need more retries? Each answer points me toward a different part of the service. A count of “slow jobs” alone gives me little basis for deciding where to look.

I’ll use a small set of synthetic records so I can work out the answers by hand and share the comparison. The same question and records go to both approaches. That lets me see, for example, whether the specialist notices an ambiguity the assistant missed.

## What I need to decide

| Decision | Evidence I need |
| --- | --- |
| Can I act on this answer? | What was counted, over which period, and enough evidence to reproduce the count. |
| When is a specialist worth using? | Questions where only one approach succeeded, where both succeeded, and where both failed. Include waiting time and total usage for each. |
| Can I trust either approach with the data? | Both can inspect only the records I make available, and neither can change them. |
| What happened when a result changed? | One place to compare earlier and later answers or look up an individual request and see where it slowed down or failed. |

## Delivery and success

First, I’ll verify the example by hand. Then I’ll try both approaches locally and compare them on ten pairs of questions. The outcome is a recommendation grounded in those cases, including ties and failures. A tie still helps me decide whether the extra work is worthwhile.

Hosted access is a separate milestone. I want to repeat a question from Claude and an OpenAI client and verify that access limits hold for both approaches. Before extending the findings to another domain, I’d want someone who knows that work to supply a real question and judge whether the answer helps them make a decision. The repeatable pilot checks are in the [product requirements](PRD.md).
