---
name: theme-lens
description: Internal lookup - finds every mention of one topic across all conversations, with who said it and the revenue behind it. Called by theme-aggregation, voice-of-customer and pricing-intel. Not used directly.
---

# Theme lens

One topic, everywhere. Other skills call this.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Today (no shared taxonomy yet)
1. `search_whole_book(question)` → semantic matches with quotes and citations ("not indexed" → `find_in_calls` on the relevant deals' file_ids)
2. `query_table` on the relevant primitive table for structured hits
3. Join back to `crm_deals` for amounts via the file→deal links
4. Count with `count_mentions(terms, deal_ids, side="buyer")` — terms are short
   words, `{label: [synonyms]}` to group variants. It counts every call and
   email on those deals, so `deals` is a real count, not a floor on what
   passages showed. Quote with `find_in_calls`; **never count from passages**
5. If step 2 is empty, keep the search hits — that is the answer, not a miss.
   If step 1 is empty too, say you searched both the table and the transcripts.

Use each hit's own `citation_url` / `(source: ...)` on that `[Citation N]` line.
Never attach Citation N's URL to a different chunk. Skip `file_id`s that start
with `Table:` — they have no transcript page.

## Once the taxonomy exists
`get_themes(window, filters)` replaces steps 1-2 and returns proper counts.

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
