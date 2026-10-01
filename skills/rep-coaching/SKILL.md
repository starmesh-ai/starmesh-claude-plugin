---
name: rep-coaching
description: How a rep is performing and what to work on - talk ratio, discovery questions, next-step discipline, objection handling, against the team median. Use for "how is X doing", "what should X improve", "is any rep dominating their calls". Talk ratio works today; question rate is not measured yet.
---

# Rep coaching

**Status: partial.** Deal level and talk ratio work. Question rate isn't measured yet.

Load `starmesh-core` and `person-lens` together in one turn (skip any already loaded) — don't load one, then the other.

## Works today — deal level
1. `aggregate_table("crm_deals", group_by=["owner_user_id"])` — book size, win rate,
   average deal size, cycle length
2. Next-step discipline on their deals, from the hygiene primitive
3. Compare each against the team median from the same aggregate

## Works today — talk ratio
1. Every call's recording_id: `query_table()` on the `*recording_deal_links*`
   table (whole book), or `list_transcripts(deal_id)` (one deal).
2. `talk_share(file_ids)` — up to 30 per call, in parallel if more. It counts
   words per speaker in code and tags the rep by matching speaker names to
   `crm_users.full_name`. `by_rep` is the per-rep answer: buyer share pooled
   over their calls, plus the min/max call.
3. Report buyer vs seller share per rep, the team figure, and any call where
   the seller side is well above the rest. Cite the `citation` block for
   numbers and a call's `citation_url` when pointing at it.

Never estimate talk ratio by reading transcripts, and never use the LLM-made
`buyer_talk_ratio_primitives` tables — `talk_share` is the source.
Attribute a call to the rep `talk_share` found speaking on it, not to the deal
owner — the owner may not have been on it. Calls with no CRM user speaking, or
with several, are listed in the `note`; say how many were left out.

## Not measured yet
Question rate and discovery quality per rep: nothing counts questions per
speaker. Say so; don't estimate.

## Answer
Headline: one thing going well, one thing to work on.
Table: metric, this rep, team median. Then specific calls to listen to with
citation urls. Then what's not measurable yet and why. Say how many deals
have calls at all — usually far fewer than the deals in the CRM.

Coaching needs an example. Never give feedback without a call to point at.

Budget: 15 tool calls.
