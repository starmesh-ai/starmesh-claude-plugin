---
name: account-health
description: The story of one account - what's happened, what changed, what's open, what to do. A narrative answer, not a dashboard. Use for "how is Acme doing", "brief me on this account before my call".
---

# Account health

**Status: works, gets better as other pieces land.**

Load `starmesh-core`, then `account-lens`.

## Answers
"How's Acme doing?" · "Brief me before my call" · "What's the state of this
relationship?"

This is the one skill where **prose is the answer**, not a table.

## Method
1. `account-lens` for the full picture
2. Build a timeline: what happened, in order, with dates
3. Identify what *changed* — new people, dropped people, shifted tone, new asks
4. Pull open items: commitments not done, objections not resolved, questions not
   answered
5. Quote the moments that matter

## Structure the narrative
- Where things stand, in two sentences
- What's happened recently, in order
- What changed and why it matters
- What's open and who owes what
- What you'd do next

## Refuse
- No linked conversations → CRM-only summary, clearly labelled as such
- Say when the account's CS conversations may be invisible (no deal link)

## Rules
Every spoken claim gets that quote's own citation url; CRM facts get the query
citation from that tool result. Don't smooth over gaps — "nothing since March"
is a finding, not a blank. If primitives are empty, still pull transcripts
before saying the account is quiet. Follow-ups asking what someone said must
call `get_transcript` / `search_transcripts`, not rephrase the earlier table.

Budget: 20 tool calls.
