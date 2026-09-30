---
name: deal-lens
description: Internal lookup - gathers everything known about one deal (CRM record, linked calls and emails, extracted signals, timeline). Called by deal-health, rep-coaching and win-loss-patterns. Not used directly.
---

# Deal lens

Everything about one deal. Other skills call this; users don't.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Steps
1. Deal id: use the one you were given (e.g. the chat's `<dashboard_scope>`).
   Otherwise `find_deal(query)` → deal_id. Multiple matches → ask.
2. `get_deal_context(deal_id)` → in one call: CRM record (deal, account,
   owner, contacts), linked calls and emails with link confidence, and
   evidence + extracted signals for the 3 most recent (`max_files` up to 5).
   Don't also call `get_deal_record` / `list_transcripts` / `list_emails` —
   it already ran them.
3. `get_transcript(file_id)` only for a file you need beyond those — an
   older call, or more of the text. If `evidence` is empty, still
   `find_in_calls` those file_ids for the user's question — empty
   primitives ≠ no conversation
4. Optional: `query_table` on a primitive table filtered by those file_ids for
   one signal type across every call. Zero rows → `find_in_calls`, don't stop

Make independent calls in the same turn (e.g. several `get_transcript`s) —
they run in parallel.

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

Budget: 8 tool calls.
