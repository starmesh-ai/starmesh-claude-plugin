---
name: deal-health
description: Assess whether a specific deal is real and moving - open commitments, unresolved objections, contact gaps, stakeholder breadth - with quotes. Use for "how is the Acme deal doing", "is this deal at risk", "what's blocking X". Works on today's data.
---

# Deal health

**Status: works today.** No missing dependencies.

Load `starmesh-core` and `deal-lens` together in one turn (skip any already loaded) — don't load one, then the other.

## Answers
"How's the Acme deal?" · "Is this deal actually moving?" · "What's blocking it?"

Not: win probability (that's a model), or forecast accuracy.

## Method
1. `deal-lens` for the deal
2. Check five things, each with evidence:
   - **Commitments** — next steps agreed, any past their date
   - **Objections** — raised and not resolved, as of their call date
   - **Contact gap** — days since the last call or email
   - **Stakeholder breadth** — one champion, or several people engaged
   - **Direction** — comparing the last two calls, better or worse
3. Quote the specific moment for anything you assert. If a check's primitive
   table is empty, `find_in_calls` on the linked file_ids before
   concluding that check is clean. Table-empty ≠ "no objection / no commitment"

## Refuse
- Fewer than 2 conversations → say the deal has too little to read
- All links below 0.5 confidence → say the calls may not belong to this deal

## Answer
Headline: moving / stalling / not enough signal — and the single biggest reason.
Then the five checks as a table. Then quotes with citation urls. Then what would
change your read.

Never output a score. Explain instead.

Budget: 15 tool calls.
