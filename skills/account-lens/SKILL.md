---
name: account-lens
description: Internal lookup - gathers everything about one account across all teams, not just its sales deals. Called by account-health, churn-signals and csm-coaching. Not used directly.
---

# Account lens

One account, whole picture. Other skills call this.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Steps
1. `find_account(query)` → account_id
2. `get_account_context(account_id)` → in one call: the account, every deal
   (open first), linked calls/emails for the first 3 deals (`max_deals` up
   to 5), and evidence from the 3 newest files (`max_files` up to 5)
3. More than 5 deals: `get_deal_context(deal_id, max_files=2)` for the rest,
   all in the same turn so they run in parallel
4. `get_transcript` only for a file those didn't read. Empty `evidence`
   still means read `content` / search — don't treat "no primitive rows" as silence
5. `find_in_calls(file_ids, keywords)` across those deals' file_ids for specific
   themes, and whenever a primitive `query_table` came back empty or the user asks what was said

## Returns
- Deals: open, won, lost, with amounts
- Every conversation tied to the account, newest first
- Days since last contact, gaps and reply times: `engagement_cadence(deal_ids)`
  over the account's deals — don't compute them from dates
- Who's involved and who's gone quiet
- Recent signals with quotes

## Gaps to state plainly
- Conversations only link to **deals** today, so a support or CS call with no
  deal attached is invisible. Say so when it matters.
- No renewal date or ARR available yet — don't imply timing you can't see.

Budget: 18 tool calls.
