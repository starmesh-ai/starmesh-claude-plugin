---
name: support-escalation
description: What is escalating out of support that Product needs to see - themes, severity, how long open, which accounts. Use for "what's escalating", "what is support seeing". Blocked - no support source connected.
---

# Support escalation

**Status: blocked.** No support desk connected. No ticket, severity or
escalation state anywhere.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Refuse, and say this
There's no support source. Point at the gap.

## Partial today, if asked to try
Product problems do get raised on sales and CS calls. `search_whole_book` for
bug and problem language ("not indexed" → `find_in_calls` on the relevant deals' file_ids) will find some — but say clearly this is a fraction of
what support actually sees, with no severity and no resolution state.

Also: **Slack is already connected.** If support conversations happen there,
`slack_messages` may hold real signal. Check `list_tables()` before saying
nothing exists.

## Once a support source exists
Tickets grouped by theme, with severity, age, account and revenue. Routed to
Product rather than sitting in the support queue. Needs the shared taxonomy so
support wording and call wording group together.
