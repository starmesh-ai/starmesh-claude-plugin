---
name: rep-coaching
description: How a rep is performing and what to work on - talk ratio, discovery questions, next-step discipline, objection handling, against the team median. Use for "how is X doing", "what should X improve". Call-behaviour parts blocked until speaker resolution exists.
---

# Rep coaching

**Status: partial.** Deal-level works. Call-behaviour needs speaker resolution.

Load `starmesh-core`, then `person-lens`.

## Works today — deal level
1. `aggregate_table("crm_deals", group_by=["owner_id"])` — book size, win rate,
   average deal size, cycle length
2. Next-step discipline on their deals, from the hygiene primitive
3. Compare each against the team median from the same aggregate

## Blocked — call behaviour
Talk ratio, question rate and objection handling are computed **per call**, and
nothing resolves who was speaking to a person. Refuse these and name the gap.

Don't attribute a call's talk ratio to the deal owner — the owner may not have
been on it.

## Answer
Headline: one thing going well, one thing to work on.
Table: metric, this rep, team median. Then specific calls to listen to with
citation urls. Then what's not measurable yet and why.

Coaching needs an example. Never give feedback without a call to point at.

Budget: 15 tool calls.
