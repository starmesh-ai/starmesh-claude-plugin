---
name: account-lens
description: Internal lookup - gathers everything about one account across all teams, not just its sales deals. Called by account-health, churn-signals and csm-coaching. Not used directly.
---

# Account lens

One account, whole picture. Other skills call this.

Load `starmesh-core` first.

## Steps
1. `find_account(query)` → account_id
2. `query_table("crm_deals", filters={"account_id": ...})` → every deal, open and closed
3. For each deal: `list_transcripts` + `list_emails`
4. `get_transcript` on the most recent few across all of them. Empty `evidence`
   still means read `content` / search — don't treat "no primitive rows" as silence
5. `search_transcripts(text, filters={"deal_id": ...})` for specific themes, and
   whenever a primitive `query_table` came back empty or the user asks what was said

## Returns
- Deals: open, won, lost, with amounts
- Every conversation tied to the account, newest first
- Days since last contact
- Who's involved and who's gone quiet
- Recent signals with quotes

## Gaps to state plainly
- Conversations only link to **deals** today, so a support or CS call with no
  deal attached is invisible. Say so when it matters.
- No renewal date or ARR available yet — don't imply timing you can't see.

Budget: 18 tool calls.
