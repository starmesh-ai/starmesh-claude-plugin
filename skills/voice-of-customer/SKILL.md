---
name: voice-of-customer
description: What customers are asking for, grouped by request with how many asked, which accounts and the revenue behind each. Use for "what are customers asking for", "what should we build", "who wants X". Undercounts until a shared taxonomy exists.
---

# Voice of customer

**Status: partial.** Feature requests are extracted from **email only** today,
not calls. So most of the signal is missing.

Load `starmesh-core` and `theme-lens` together in one turn (skip any already loaded) — don't load one, then the other.

## Method
Order (core §0b): request table, then `deal_memory_bullets`, then call quotes,
then `count_mentions`. Tags give the list; transcripts give the evidence.

1. `list_tables()` → find the feature request table; note it's email-sourced
2. `aggregate_table` grouped by request → frequency
3. Find the same asks **in calls**, which the extractor missed — this is where
   most of it lives:
   a. If `list_tables` shows a `deal_memory_bullets` table (one row per fact
      pulled from each call, all calls, deal-linked or not), read it first:
      `query_table(that table, filters={"category": ["feature", "risk",
      "timeline", "pain_point", "open_question"], "importance_score": {"gte":
      0.6}}, columns=["file_id", "deal_id", "interaction_timestamp",
      "category", "importance_score", "bullet_text"], limit=300)`. Each
      `bullet_text` says who asked ("buyer-side requested ...") and for what;
      `feature` rows are the asks, `risk` rows the blockers, `timeline` rows the
      deadlines. Near-duplicate bullets are one ask, not several. `deal_id`
      `__NO_DEAL__` means the call isn't linked to a deal: take the account from
      the bullet text.
   b. For a quote or anything the bullets miss: `search_whole_book` for the
      wording ("not indexed" → `find_in_all_calls(keywords)`, which reads every
      call, linked to a deal or not), or `find_in_calls` on the `file_id`s the
      bullets came from.
   c. `count_mentions({ask: [synonyms]}, deal_ids, side="buyer")` for how many
      deals asked. Never count from passages
4. Merge by hand, show the merges
5. Join to accounts and revenue

## Who counts as the customer
"Pilots", "trials", "early customers" mean the accounts on live engagements, not
only deals with the word "pilot" in the name: read the open/in-progress deals
(`query_table` on the CRM deals) and take each one's account. Then
`find_in_all_calls` with the account's name plus its product words (e.g. the
account name, "sandbox", "integration", "deployment") — one call per few
accounts, not generic words like "need"/"want".

Calls linked at low confidence, rejected, or to no deal still count: never limit
a book-wide question to the calls on some deals. If speakers are only "Me" /
"System audio" (no buyer labels), the asks come from the team's own calls: report
them per account as team-reported, say so once, and don't drop them for sounding
internal. A blocker the team hit (e.g. no sandbox access) is an ask too.
Group the answer by pilot/account.

## Say this in every answer
Requests are extracted from emails only. Calls are searched by wording, so call
mentions are undercounted. **Numbers are a floor.**

## Rank by accounts, not mentions
One account asking five times is not five customers. Report distinct accounts
first, mentions second, revenue third.

## Refuse
Don't rank by revenue alone — one large account's wish is not a product
priority. Show all three columns and let the reader weigh them.

## Answer
Headline: top 3 asks by distinct accounts.
Table: request, accounts, mentions, revenue, first and last seen.
Then quotes with citation urls. Then the caveat.

Budget: 20 tool calls.
