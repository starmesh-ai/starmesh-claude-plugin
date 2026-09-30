---
name: forecast-accuracy
description: Scores past forecasts against what actually happened - by rep and by how far out the call was made. Use for "are our forecasts any good", "who sandbags", "how much slips". Blocked until deal_history exists.
---

# Forecast accuracy

**Status: blocked.** Needs `deal_history` — a daily snapshot of stage, amount
and close date. Nothing today records what a deal looked like last month, so
there is no stored forecast to score.

Load `starmesh-core` in the same turn as your first data calls if it isn't loaded yet — don't wait on it.

## Refuse, and say exactly this
There's no history table. Current-state CRM cannot answer this — a deal whose
close date moved three times looks identical to one that never moved. Point at
`deal_history` as the fix.

**Do not approximate.** No proxy from current data is honest here.

## Once deal_history exists
1. `compare_periods` — forecast at T vs outcome, by rep and horizon
2. Slip rate: how often close dates move, and by how much
3. Amount drift: booked vs forecast at each stage
4. Break down by rep, by stage entered, by quarter

Metric: `forecast_accuracy` in `_metrics/`.
