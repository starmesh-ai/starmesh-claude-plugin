---
name: person-lens
description: Internal lookup - gathers one rep or CSM's calls and behaviour numbers against the team median. Called by rep-coaching and csm-coaching. Talk ratio and question rate work via talk_share.
---

# Person lens

One rep or CSM. Other skills call this.

**Status: partial.** Calls are attributed to a person by `talk_share`, which
matches transcript speaker names to `crm_users.full_name`.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Steps
1. `query_table("crm_users", ...)` → resolve the person
2. Their owned deals via `crm_deals.owner_user_id`
3. Their calls: recording_ids from the `*recording_deal_links*` table (or
   `list_transcripts` per deal), then `talk_share(file_ids)` — the calls whose
   `reps` include this person are theirs, whoever owns the deal
4. Behaviour per call: talk ratio and question rate
   (`seller_questions_per_100_words`) from `talk_share`; next-step discipline and
   objection handling from the primitives tables, joined on file_id
5. Team median: the same numbers for every rep in `talk_share`'s `by_rep`, and
   `aggregate_table` for deal-level ones

## Say it, don't guess it
- Question rate is counted (sentences ending in "?"); discovery quality is not
  measured. Name the gap.
- Speakers `talk_share` marks "unknown" (not a CRM user, not labelled buyer):
  report them, don't count them as buyers.
- Deal-owner numbers are deal level, not call behaviour. Say which is which.
