# Metric definitions

One file per metric. Skills cite a metric by name and never write a formula
inline — so a number means the same thing every time it's reported.

A skill that can't find a metric here refuses. It does not invent one.

```yaml
id: pipeline_coverage
question: open pipeline against quota for a period
grain: [owner, quarter]
formula: sum(crm_deals.amount where is_open) / ref_targets.quota
sources:
  - crm_deals      # rows are 2x duplicated, use aggregate_table
  - ref_targets    # does not exist yet
window: current fiscal quarter unless asked otherwise
refuse_if:
  - no quota row for that owner and period
  - fewer than 3 open deals in the group
known_gap: deals with null amount are excluded - report that count
owner: sales-ops
version: 1
```

Changing a definition bumps `version` and re-runs the skill's tests.
