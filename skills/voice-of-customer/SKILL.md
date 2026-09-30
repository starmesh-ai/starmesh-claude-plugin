---
name: voice-of-customer
description: What customers are asking for, grouped by request with how many asked, which accounts and the revenue behind each. Use for "what are customers asking for", "what should we build", "who wants X". Undercounts until a shared taxonomy exists.
---

# Voice of customer

**Status: partial.** Feature requests are extracted from **email only** today,
not calls. So most of the signal is missing.

Load `starmesh-core` and `theme-lens` together in one turn (skip any already loaded) — don't load one, then the other.

## Method
1. `list_tables()` → find the feature request table; note it's email-sourced
2. `aggregate_table` grouped by request → frequency
3. `search_whole_book` for the same asks **in calls** ("not indexed" → `find_in_calls` on the relevant deals' file_ids),, which the extractor
   missed — this is where most of it lives
4. Merge by hand, show the merges
5. Join to accounts and revenue

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
