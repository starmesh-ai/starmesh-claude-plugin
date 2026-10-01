---
name: churn-signals
description: Which accounts are drifting toward churn and which are opening up for expansion, ranked with reasons and quotes. Use for "who's at risk", "which accounts are slipping", "where's expansion". Partial - needs renewal dates for timing.
---

# Churn and expansion signals

**Status: partial.** Signals work. Timing doesn't — no renewal dates.

Load `starmesh-core` and `account-lens` together in one turn (skip any already loaded) — don't load one, then the other.

## Method
For each account, gather and weigh in the answer — not in a score:
- **Contact gap** — `engagement_cadence(deal_ids)` over the account's deals:
  `days_since_last` vs that account's own `max_gap_days`, touches in the last
  30/90 days, and median buyer reply hours. One call for all deals; never
  compute gaps from dates yourself
- **Who went quiet** — a champion who stopped appearing
- **Sentiment direction** — across their last few calls, not one
- **Competitor mentions** — recent and by whom: `count_mentions(competitors,
  deal_ids, side="buyer")`
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
primitive tables are empty, `find_in_calls` on that account's deals' file_ids
before ranking it as quiet.

Budget: 25 tool calls.
