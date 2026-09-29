---
name: campaign-attribution
description: Which marketing campaigns produce real pipeline and closed revenue. Use for "which campaigns work", "where does good pipeline come from". Blocked - no marketing source connected.
---

# Campaign attribution

**Status: blocked.** No marketing connector exists. There is no campaign, lead,
or touch object anywhere in the data.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Refuse, and say this
Nothing connects campaigns to deals. If CRM carries a `lead_source` field, you
can report deals grouped by it — but say plainly that a single free-text source
field is not attribution, and that it misses every touch before the last one.

Check first: `get_table_schema("crm_deals")` for a lead source column.

## Once a marketing source exists
Campaign → lead → contact → deal, joined in SQL. This is not a judgment call and
should not be a skill that reasons — it's a query with a fixed definition.
