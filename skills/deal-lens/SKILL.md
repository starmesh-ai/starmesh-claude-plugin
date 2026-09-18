---
name: deal-lens
description: Internal lookup - gathers everything known about one deal (CRM record, linked calls and emails, extracted signals, timeline). Called by deal-health, rep-coaching and win-loss-patterns. Not used directly.
---

# Deal lens

Everything about one deal. Other skills call this; users don't.

Load `starmesh-core` first.

## Steps
1. `find_deal(query)` → deal_id. Multiple matches → ask.
2. `get_deal_record(deal_id)` → deal, account, owner, contacts
3. `list_transcripts(deal_id)` and `list_emails(deal_id)` → file ids + link confidence
4. `get_transcript(file_id)` for the most recent 3-5 → evidence quotes and
   extracted signals. If `evidence` is empty, still use `content` and
   `search_transcripts` for the user's question — empty primitives ≠ no conversation
5. Optional: `query_table` on a primitive table filtered by those file_ids for
   one signal type across every call. Zero rows → `search_transcripts`, don't stop

## Returns
- CRM state: stage, amount, close date, owner, age
- Conversations: how many, most recent, gap since last
- Signals: open/overdue next steps, unresolved objections, risk language,
  stakeholder count — each with the call it came from
- Link confidence per file

## Watch for
- Link confidence below 0.5 — flag it, the call may belong to another deal
- Zero linked conversations — say so; CRM-only answers are weak
- Emails and calls both matter; don't use only transcripts
- Cite CRM / list results with the tool's query `citation_url` (add `#row-N`
  for one row). Cite spoken words with that evidence item's quote `citation_url`.
  Never swap them

Budget: 15 tool calls.
