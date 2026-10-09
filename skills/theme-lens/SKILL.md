---
name: theme-lens
description: Internal lookup - finds every mention of one topic or theme across all conversations (topic-modeling tables first, then transcripts), with who said it and the revenue behind it. Called by theme-aggregation, voice-of-customer and pricing-intel. Not used directly.
---

# Theme lens

One topic, everywhere. Other skills call this.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Order (core §0b): topic tables first, then transcripts
1. **Topic tables.** `list_tables()` → the topic / topic-modeling primitive
   tables. `aggregate_table` / `query_table` for the topic: distinct accounts,
   mentions, trend by month, filtered by window. Join to `crm_deals` through
   the file→deal links for amounts. **If rows come back, these are the counts**
   — report them as tag-based.
2. **Quotes — always, in the same answer.** The topic table holds a
   paraphrased `summary` and `keywords`, not what was said: never present a
   `summary` as a quote. Take the topic's `file_id`s and its `keywords` from
   step 1 and call `find_in_calls(file_ids, keywords)` — that returns the
   verbatim passages with their quote citations. If the rows carry
   `timestamp_start`/`timestamp_end`, `get_transcript` around that span.
   Short topic words plus synonyms, never a sentence.
3. **What the tags missed.** Only now `search_whole_book(question)` ("not
   indexed" → `find_in_all_calls(keywords)`: it reads every call, linked to a
   deal or not; say how many it searched).
4. **Wording-based count.** If the user's topic isn't in the tags, or step 1 is
   empty / "other"-only: `count_mentions(terms, deal_ids, side="buyer")`
   (`{label: [synonyms]}` to group variants). It counts every call and email
   on those deals. Quote with `find_in_calls`; **never count from passages**
5. Step 1 empty and step 3 empty → say you searched both the table and the
   transcripts. Search hits alone are still an answer, not a miss.

Say which numbers are tag-based (step 1) and which are wording-based (step 4).
Never add the two together.

Use each hit's own `citation_url` / `(source: ...)` on that `[Citation N]` line.
Never attach Citation N's URL to a different chunk. Skip `file_id`s that start
with `Table:` — they have no transcript page.

## Once the taxonomy exists
`get_themes(window, filters)` (not available yet) will replace steps 1 and 4 and returns proper counts.

## Returns
- Every mention, with quote and citation
- How many distinct accounts, not just how many mentions
- Revenue attached to those accounts
- Trend over time

## The warning to always give
Without the shared tag list, matching is by wording. "Integration issues" and
"data import failures" won't group. **Counts are a floor, not a total** — say
this every time, in the answer. The count is exact for the words you passed;
the floor is the words you didn't think of.

Budget: 10 tool calls.
