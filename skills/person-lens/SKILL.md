---
name: person-lens
description: Internal lookup - gathers one rep or CSM's calls and behaviour numbers against the team median. Called by rep-coaching and csm-coaching. Blocked until speaker resolution exists.
---

# Person lens

One rep or CSM. Other skills call this.

**Status: blocked.** Call participants aren't resolved to people yet, so
per-call behaviour numbers can't be attributed to a person. Until then, this
skill refuses and says why.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## What it will do
1. `query_table("crm_users", ...)` → resolve the person
2. Their owned deals via `crm_deals.owner_id`
3. Their calls via participant resolution *(missing)*
4. Behaviour signals per call: talk ratio, question rate, next-step discipline,
   objection handling
5. `aggregate_table` for the team median to compare against

## Partial fallback available today
Deal-owner level only: their book, win rate, deal sizes, next-step hygiene on
deals they own. Say clearly that this is deal-level, not call-behaviour level.

## Refuse
Any request for talk ratio, question rate or discovery quality by person —
those need speaker resolution. Name the gap.
