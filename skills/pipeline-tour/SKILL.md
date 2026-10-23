---
name: pipeline-tour
description: A quick first look at the book in Starmesh - pipeline by stage, the deals that need attention this week and why, and what buyers keep raising on calls, with sources. Use right after the user installs or connects Starmesh, or for "try Starmesh", "what can you do", "show me around", "what's in my pipeline", "which deals need attention this week". Works on sample data and on a connected book.
---

# Pipeline tour

**Status: works today**, on sample data (free plan) and on a connected book.

Load `starmesh-core` in the same turn.

## Method
1. `starmesh_status()`. If it says setup is not done and `sample_mode` is
   false, load `setup` instead and stop here.
2. In one turn, in parallel:
   - `aggregate_table` over `crm_deals`: open deals by stage, count and total amount
   - `query_table` on `crm_deals`: open deals closing in the next 30 days, by amount
   - `search_whole_book` for objections and risks buyers raised recently
3. Pick the 3 deals that most need attention (closing soon with no recent
   call, an open objection, or a slipped next step). For each, run
   `engagement_cadence([deal_id])` and quote one line from a call.

## Answer
Follow core §8: verdict first, the twist, one next move.
Short. One table (stage, deals, total), then the 3 deals as bullets with one
reason and one cited quote each, then the top 2-3 themes from calls. On sample
data, label it sample data once. End with 3 questions they could ask next,
such as "How is the <deal> deal doing?" or "Why do we lose?".
