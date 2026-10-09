---
name: starmesh-core
description: Shared rules for every Starmesh analysis skill - entity resolution, citation kinds (never mix quote URLs with query URLs), and falling through to transcripts when tables are empty or the user asks for specifics. Load it in the same turn as the analysis skill (not before it). Not used on its own.
---

# Starmesh analysis — shared rules

Every Starmesh skill follows these.

## 0. Speed — the user waits on every round trip
- Calls that don't depend on each other go in the same turn: they run in
  parallel. Load skills in the same turn as the first data calls.
- Use the one-call tools: `find_deal(query, include_context=true)` resolves
  and reads a deal at once; `get_deal_context(deal_id)` for a known deal;
  `get_account_context(account_id)` for an account.
- CRM tables can be named directly (`crm_deals`, `crm_accounts`,
  `crm_contacts`, `crm_users`, `crm_meetings`) — skip `list_tables` /
  `get_table_schema` unless a name or column is actually unknown.
- Stop calling tools once you can answer what was asked.

## 0b. Source order — always this sequence
1. **Structured first.** Primitive tables (`query_table` / `aggregate_table`),
   CRM tables, `get_*_context`. They give the numbers, the candidate list, and
   which `file_id`s to read. Never skip this step.
2. **Then the conversation.** `find_in_calls` / `get_transcript` on the
   `file_id`s step 1 returned: quotes, and what the labels missed.
3. **Then book-wide search.** `search_whole_book` / `find_in_all_calls` only for
   what steps 1-2 can't reach (unlinked calls, wording the tags missed).
4. **Code tools** (`count_mentions`, `price_points`, `talk_share`,
   `engagement_cadence`) whenever a number comes from text. Never count yourself.

Rows in the table → the table's numbers are the answer; transcripts add quotes,
not totals. Do step 2 in the same answer — don't stop at the table and offer
quotes as a follow-up. Sample data (`data_status: sample`) is still data: answer
from it, labelled once, rather than refusing. Table empty, "other"-only, or under ~5 rows → say so in one
sentence, then go to step 2/3. Mark each claim as tag-based or
transcript-based. A skill may reorder this only if it says why.

## 1. Resolve the entity first
Never guess an ID from a name. `find_deal("Acme")` or `find_account("Acme")`
first. If more than one matches, ask which — don't pick. An id you were
given outright (the user's message, or the chat's `<dashboard_scope>`) is
already resolved — use it, don't look it up again.

For one deal, `get_deal_context(deal_id)` returns the CRM record, linked
calls and emails, and recent evidence in a single call (`find_deal(...,
include_context=true)` returns it with the match). For one account,
`get_account_context(account_id)`. Prefer them to walking `get_deal_record` →
`list_transcripts` → `list_emails` → `get_transcript`.

## 2. Never add up rows by hand, or count from text
Use `aggregate_table`. It dedupes and computes in SQL.
`query_table` is for looking at rows, never for totals.

Counts, sums and times that come from transcript or email text are code-tool
output, never your own reading of passages (`find_in_calls` shows at most 15):

| Need | Tool |
|---|---|
| How many deals mention X, who raised it | `count_mentions(terms, deal_ids, side)` |
| Dollar amounts, discounts, quote-vs-counter gaps | `price_points(deal_ids)` |
| Talk ratio, question rate | `talk_share(file_ids)` |
| Days since last touch, gaps, reply times | `engagement_cadence(deal_ids)` |

The tool counts; you label and judge. Quote with `find_in_calls`.

- No term list to count (e.g. "which products", "which competitors")? Don't
  refuse for lack of a catalog. Find candidate names in the calls first —
  `search_whole_book`, or if it errors, `find_in_calls` / `get_transcript` on 2-3
  of the deals' files — then `count_mentions` them. Skip generic words that
  match everyday talk ("voice", "reviews"). Only ask the user for names after
  trying that.
- `price_points` output is large: call it for about 3 deals at a time, in the
  same turn so the calls run in parallel, instead of one call for every deal.

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
- Live on the item: `evidence[].citation_url` from `get_transcript`,
  `passages[].citation_url` from `find_in_calls`, or
  `citations[].citation_url` / the `(source: ...)` on that `[Citation N]` line
  from `search_whole_book`
- The quote in the answer must come from the same `evidence_text` / `passage` /
  `chunk_text` that URL was minted for. Citation 3's URL never backs Citation 7's words
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
See §0b for the order. Tables and primitives are an index, not the whole answer.

**Empty structured data is not "nothing happened."**
If `query_table`, `aggregate_table`, or a primitive table returns no rows, or
`get_transcript`'s `evidence` list is empty, do not stop and do not refuse
yet. Fetch unstructured content:

1. `list_transcripts` and `list_emails` for the resolved deal (or each deal
   under the account)
2. `find_in_calls(file_ids, keywords)` on those file_ids — see §10 for keywords
3. `get_transcript(file_id, start_line=...)` to read around a passage, or on the
   most relevant files when you need the whole conversation. `content` is paged
   (`next_start_line`); quote from `content` only after you have a quote
   citation for that span (a `find_in_calls` passage mints one)

Then answer from those quotes. If tables *and* transcripts are empty, say you
searched both, name what you searched, and stop. That is a refusal. "Not in
the objections table" is not.

**Follow-ups after a table answer must go to transcripts.**
When the user asks for specifics after a structured answer — what was said,
the quote, who raised it, which call, a name, a date, a competitor, an
objection in their own words — call `find_in_calls` and/or `get_transcript`
before answering. Do not rephrase the previous table. Primitive fields
(`objection_severity`, `proposed_action`, topic tags) are labels, not quotes.

**Reserve budget.** Keep at least 3 tool calls for this fallback. If tables
already used the budget, say so and still make the one most useful
`find_in_calls` call rather than answering from memory.

## 6. Primitive verdict fields are as-of-date, not current
Fields like `objection_severity`, `resolution_rate`, `proposed_action` were
computed when the call was processed, from what was known *then*. Report them as
"as of <call date>". Never as today's state.

**Check dates against the deal.** If a call or email is dated after the deal's
`close_date` on a closed deal, say so plainly ("this call is dated two months
after the deal closed lost") and don't present it as what led to the outcome —
either the link or a date is wrong. Same for a call before the deal was created.

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

## 10. Finding what was said: `find_in_calls` vs `search_whole_book`
- Known deal or account → `find_in_calls`. Unknown deal, book-wide question → `search_whole_book`.
- `find_in_calls` needs file_ids: `list_transcripts` / `list_emails` first. Pass up to 20 at once.
- `keywords` = 3-6 short words or two-word terms plus synonyms, never a sentence.
  Good: `["retry", "call back", "missed", "voicemail", "attempts"]`.
  Bad: `["what did they say about retrying missed callbacks"]`
- No passages → retry once with different words before saying it never came up.
  Even then say "not found with these words", not "not discussed".
- `search_whole_book` only covers indexed deals. "Not indexed" error → switch to
  `find_in_calls`; don't retry `search_whole_book`.
- Book-wide question and "not indexed" → `find_in_all_calls(keywords)`: one call
  that reads every call in the dataset, including calls linked to no deal. Use
  topic words ("sandbox", "pilot"), not "need"/"want". Say how many calls it
  searched. Don't answer a book-wide question from only a few deals' calls.

## 11. Know what data you are reading
`starmesh_status()` reports `data_status`. It decides what you may claim:

| `data_status` | Meaning | Do |
|---|---|---|
| `sample` | Signed out or chose sample | Say once it is illustrative sample data, not their pipeline. Offer `onboarding_url` |
| `not_ready` | Signed in, nothing readable yet | Say it is syncing or not connected; don't answer from sample or memory. Point at `onboarding_url` |
| `ready` | Their own data | Answer normally |
| `trial_expired` | Past `query_until` | Say so; point at `upgrade_url`; don't answer from stale tables |

- Trial data is a one-time pull of a limited window (`trial_window_start` →
  `trial_window_end`). State that window when a count or trend depends on it —
  "nothing before <start>" is a limit of the import, not a finding.
- Only the user's own book is visible. Colleagues' deals may be absent: say
  "in your book", not "at the company", unless the data shows the whole team.
- `action_required` (e.g. `choose_crm_user`) means a sync is stopped. Raise it
  before analysis; numbers will be incomplete until it is resolved.
- Thin early data (a few deals, calls from one week): say how much you have
  before drawing a pattern. Don't pad with general sales advice.
- A first-time user may not know what to ask. After the first answer, offer 2-3
  follow-ups this data can actually support.
