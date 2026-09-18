---
name: churn-signals
description: Which accounts are drifting toward churn and which are opening up for expansion, ranked with reasons and quotes. Use for "who's at risk", "which accounts are slipping", "where's expansion". Partial - needs renewal dates for timing.
---

# Churn and expansion signals

**Status: partial.** Signals work. Timing doesn't — no renewal dates.

Load `starmesh-core`, then `account-lens`.

## Method
For each account, gather and weigh in the answer — not in a score:
- **Contact gap** — days since any conversation, vs that account's own normal
- **Who went quiet** — a champion who stopped appearing
- **Sentiment direction** — across their last few calls, not one
- **Competitor mentions** — recent and by whom
- **Unresolved objections** — still open, as of their call date
- **Expansion tells** — new teams appearing, new use cases, volume questions

Rank and explain. **Never emit a churn score.** A ranked list with reasons is
more useful and more honest than a number.

## Refuse
- "Which accounts churn *this quarter*" → no renewal dates. Give the ranked list
  without timing and say why.
- Accounts with no linked conversations → list them separately as unreadable,
  don't silently drop them.

## Watch for
CS calls often have no deal attached, so `account-lens` can't see them. Say so.

## Answer
Headline: how many accounts show drift, how many show expansion.
Table: account, signals fired, last contact, ARR if known.
Then per top account: 2-3 quotes with citation urls. If an account's
primitive tables are empty, `search_transcripts` that account's deals
before ranking it as quiet.

Budget: 25 tool calls.
