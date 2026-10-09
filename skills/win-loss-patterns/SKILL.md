---
name: win-loss-patterns
description: Compare closed-won against closed-lost deals to find what actually differs - objection types, stakeholder count, next-step discipline, competitor presence, cycle length. Use for "why do we lose", "what do our wins have in common". Works on today's data.
---

# Win/loss patterns

**Status: works today.**

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Answers
"Why do we lose?" · "What's different about deals we win?" · "Do we lose more
when competitor X shows up?"

## Method
Order (core §0b): CRM stages and primitive aggregates first, code tools for
text-derived counts, transcripts last for quotes.

1. `aggregate_table("crm_deals", group_by=["stage"])` — see the real stage names
   first, don't assume "Closed Won"
2. Build two cohorts by stage filter, get deal_ids for each
3. `aggregate_table` on each primitive table, filtered to each cohort's files,
   for: objection counts and types, stakeholder count, next-step rate,
   risk language. Competitor presence: `count_mentions` per cohort's
   deal_ids (side="buyer"). Discounts and counters: `price_points` per cohort's
   deal_ids — every deal with calls, not a sample
4. Compare the two. Report the differences that are actually large.
5. `find_in_calls` on the compared deals' file_ids for quotes illustrating the
   top 2-3 differences.
   Empty primitive aggregates still get a transcript search before you
   conclude that side has no objections / competitors / next steps

## Refuse
- Fewer than 10 deals on either side → say the comparison isn't meaningful and
  give the counts
- Report how many deals had no linked conversations — they're invisible to this

## Answer
Headline: the one or two things that most separate wins from losses.
Table: metric, won, lost, gap. Then quotes. Then caveats — sample size, missing
conversations, what this can't see.

Don't claim causation. These are differences, not reasons.

Budget: 20 tool calls.
