---
name: theme-aggregation
description: What customers are collectively saying, grouped into themes with counts, trend and revenue attached. Use for "what are the top issues", "what are we hearing most". Undercounts until a shared topic taxonomy exists - always say so.
---

# Theme aggregation

**Status: partial, and it fails quietly.** Topics are free text today. "Integration
issues" and "data import failures" never group. Counts come out low with no error.

Load `starmesh-core`, then `theme-lens`.

## Method today
1. `list_tables()` → find the topics primitive table
2. `aggregate_table` on it grouped by topic → raw frequency
3. Merge obvious duplicates **by hand and show what you merged**
4. `search_whole_book` per theme ("not indexed" → `find_in_calls` on the relevant deals' file_ids) for quotes and to catch wording the tags
   missed. If the topics table is empty or "other"-only, search is the
   answer — don't report "no themes"
5. Join to deals for revenue behind each theme

## The warning — give it every time
State in the answer: counts are a floor, not a total. Grouping is by wording.
Two teams describing the same problem differently will not merge.

## Refuse
- If the merged-by-hand step changes counts by more than roughly half, say the
  grouping is too unreliable to report numbers, and give themes with quotes only.

## Once the taxonomy exists
`get_themes(window)` replaces steps 1-3 and the warning goes away. Also watch the
"other" bucket — if it grows, the tag list needs updating.

## Answer
Headline: top 3 themes by distinct accounts (not mentions).
Table: theme, accounts, mentions, revenue, trend. Then quotes. Then the caveat.

Budget: 20 tool calls.
