---
name: pipeline-coverage
description: Pipeline against quota, and how much of it is shaky. Use for "do we have enough pipeline", "what's our coverage", "how much of the quarter is at risk". Coverage blocked until quota data exists; risk works today.
---

# Pipeline risk and coverage

**Status: half works.** Risk works today. Coverage needs `ref_targets` (quotas),
which don't exist.

Load `starmesh-core`.

## Coverage — refuse without quotas
Coverage = open pipeline ÷ quota. There is no quota table.
**Never substitute a guess, a prior year, or a rule of thumb.** Say the quota is
missing and which owner/period you needed it for.

You can still report total open pipeline as a number on its own.

## Risk — works today
1. `aggregate_table("crm_deals", filters={"is_open": true}, group_by=["stage"])`
   with `sum:amount`
2. Cross-reference risk primitive tables for stalled and objection-heavy deals
3. Report: total open, amount in deals showing risk signals, and which deals
4. Always report how many open deals have a null amount — they're excluded

## Answer
Headline: total open pipeline, and how much carries risk signals.
Table by stage. Then the specific at-risk deals with the signal and a quote.
Then explicitly: "coverage unavailable — no quota data".

Budget: 15 tool calls.
