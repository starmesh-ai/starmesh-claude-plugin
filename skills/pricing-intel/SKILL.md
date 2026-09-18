---
name: pricing-intel
description: What customers say about price, discounting and competitors - which competitors appear, discount pressure over time, win rate when each shows up. Use for "what are we hearing on pricing", "who do we lose to". Works on today's data.
---

# Pricing and market intel

**Status: works today.** Best early proof of the whole setup.

Load `starmesh-core`, then `theme-lens`.

## Answers
"What are we hearing about pricing?" · "Who do we come up against?" · "Is
discount pressure getting worse?"

## Method
1. `list_tables()` → find the competitor and pricing primitive tables
2. `aggregate_table` on the competitor table, grouped by competitor name →
   frequency
3. Join to deals: win rate when each competitor is present vs overall
4. `aggregate_table` on the pricing/discount tables by month → is pressure rising
5. `search_transcripts` for quotes on the top findings. If a primitive table
   is empty, still search — missing tags are not missing talk

## Refuse
Nothing blocks this. But always report:
- Competitor names aren't normalised — "Salesforce" and "SFDC" count separately.
  Say so and merge obvious ones by hand, showing what you merged.

## Answer
Headline: the top competitor and the pricing trend.
Table: competitor, mentions, deals, win rate. Then a discount-pressure trend.
Then quotes.

Budget: 15 tool calls.
