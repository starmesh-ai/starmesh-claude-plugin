---
name: starmesh-core
description: Shared rules for every Starmesh analysis skill - entity resolution, citation kinds (never mix quote URLs with query URLs), and falling through to transcripts when tables are empty or the user asks for specifics. Load this before any other starmesh skill. Not used on its own.
---

# Starmesh analysis — shared rules

Every Starmesh skill follows these. Read before anything else.

## 1. Resolve the entity first
Never guess an ID from a name. `find_deal("Acme")` or `find_account("Acme")`
first. If more than one matches, ask which — don't pick.

## 2. Never add up rows by hand
Use `aggregate_table`. It dedupes and computes in SQL.
`query_table` is for looking at rows, never for totals.

## 3. CRM rows are duplicated 2x
`crm_deals`, `crm_accounts`, `crm_contacts`, `crm_users`, `crm_meetings`,
`crm_meeting_participants` each hold every row twice. `aggregate_table` handles
it; raw counts from `get_table_schema` do not. Use `rows_matched`, not `row_count`.

## 4. Citations — copy the URL that belongs to that fact
Two kinds. Never mix them. Wrong links almost always mean a URL from one result
was attached to a different fact.

**Query citations** back numbers, rows, and records.
- Live in that tool result's top-level `citation.citation_url` — except
  `get_transcript`, whose fan-out proof (that this call *had* an objection
  row — not the words themselves) is deliberately named `extraction_citation`
  instead, so it can never be mistaken for one of that same result's
  `evidence[].citation_url` quote links
- Tools: `find_deal`, `find_account`, `get_deal_record`, `list_transcripts`,
  `list_emails`, `query_table`, `aggregate_table`, and `get_transcript`'s
  `extraction_citation`
- One specific row: append `#row-N` where N is that row's 1-based position in
  **the list that same tool returned**, not a table you rebuilt in the answer
- Empty result: you may cite the query URL to prove nothing matched. Do not
  invent a quote to go with it

**Quote citations** back what was SAID.
- Live on the item: `evidence[].citation_url` from `get_transcript`, or
  `citations[].citation_url` / the `(source: ...)` on that `[Citation N]` line
  from `search_transcripts`
- The quote in the answer must be the same `evidence_text` / `chunk_text` that
  URL was minted for. Citation 3's URL never backs Citation 7's words
- Never put `#row-N` on a quote URL
- Skip items whose `file_id` starts with `Table:` — those are structured rows
  in the vector store, not a transcript, and have no source document

**Copy rules**
- Copy the URL from the same tool result as the fact. Never reuse an earlier
  call's URL because it looks similar, belongs to the same deal, or is handy
- Never cite a spoken claim with a query URL (SQL / rows page), or a number
  with a transcript highlight
- `https://` → markdown-link it: `([source](https://...))`
- `file://` → show as visible inline code: `(source: \`file:///...\`)`. Never
  hide a `file://` path behind link text — the client will not open it
- Integer `citation_id` (web chat) is not a URL. Do not print it as one
- If you cannot find the matching URL in that result, omit the link and say the
  claim is uncited. Do not guess, concatenate, shorten, or borrow another
  fact's link

No matching quote and no matching query URL means don't state it as sourced.

## 5. Structured first, then the actual conversation
Tables and primitives are an index, not the whole answer.

**Empty structured data is not "nothing happened."**
If `query_table`, `aggregate_table`, or a primitive table returns no rows, or
`get_transcript`'s `evidence` list is empty, do not stop and do not refuse
yet. Fetch unstructured content:

1. `list_transcripts` and `list_emails` for the resolved deal (or each deal
   under the account)
2. `get_transcript(file_id)` on the most relevant files — read `content` even
   when `evidence` is empty; quote from `content` only after you have a quote
   citation for that span (or search, which mints one)
3. `search_transcripts(text, filters={"deal_id": ...})` using the user's actual
   phrasing (and obvious variants)

Then answer from those quotes. If tables *and* transcripts are empty, say you
searched both, name what you searched, and stop. That is a refusal. "Not in
the objections table" is not.

**Follow-ups after a table answer must go to transcripts.**
When the user asks for specifics after a structured answer — what was said,
the quote, who raised it, which call, a name, a date, a competitor, an
objection in their own words — call `get_transcript` and/or `search_transcripts`
before answering. Do not rephrase the previous table. Primitive fields
(`objection_severity`, `proposed_action`, topic tags) are labels, not quotes.

**Reserve budget.** Keep at least 3 tool calls for this fallback. If tables
already used the budget, say so and still make the one most useful
`search_transcripts` or `get_transcript` call rather than answering from memory.

## 6. Primitive verdict fields are as-of-date, not current
Fields like `objection_severity`, `resolution_rate`, `proposed_action` were
computed when the call was processed, from what was known *then*. Report them as
"as of <call date>". Never as today's state.

## 7. Refuse rather than guess
- Metric has no definition in `_metrics/` → say so, don't invent one
- Required table missing → name it, stop — unless the question is about what
  was *said*, in which case go to transcripts (§5) instead of stopping
- Below the skill's minimum data after both structured *and* unstructured
  fetches → say what's missing and how much you have

A refusal is a correct answer. A confident number built on a missing quota table
is not.

## 8. Answer shape
1. One-line headline
2. Numbers (table), each with its query citation
3. Evidence — quotes, each with its own quote citation url
4. What would change this
5. If you fell through to transcripts because tables were empty, say that in
   one sentence so the reader knows the number isn't missing by accident

## 9. Stay in budget
Each skill states a max tool-call count. If you hit it, report what you have and
say what you skipped. Do not skip §5 to stay under budget if the user asked
what was said.
