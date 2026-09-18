---
name: launch-feedback
description: How a specific launch or feature landed - what customers said before and after, split by whether they had used it. Use for "how did X land", "what's the reaction to Y". Blocked - no launch dates recorded.
---

# Launch feedback

**Status: blocked.** No `ref_launches` table. Nothing records what shipped or
when, so there's no before/after to compare against.

Load `starmesh-core`, then `theme-lens`.

## Refuse, and say this
There's no launch date. "Recently" is not a window, and picking one yourself
would make the comparison meaningless. Ask for the launch date, or point at
`ref_launches`.

If the user gives a date in the question, you can proceed — use theirs, and say
you used it.

## Once ref_launches exists
1. The launch date and name from `ref_launches`
2. Claims tagged to that feature, in the window before and the window after
3. Split: mentioned by people who used it vs people who only heard about it
4. Sentiment direction, and what specifically they objected to or liked
5. Whether it showed up in any won or lost deal reasoning

Equal-length windows. Say what they were.
