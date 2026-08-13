---
description: Re-fit the remaining CCA-F study work into a new number of days (e.g. /replan 3d)
argument-hint: "[Nd | N | Nw | YYYY-MM-DD | status]"
---

Run the **replan** skill to re-pace `CCA-F-LEARNING-PLAN.md`.

The learner has `$ARGUMENTS` days left before their target completion date. Invoke the `replan` skill and follow its procedure exactly:

1. Read the plan and measure current progress (`[x]` vs `[ ]`, overall and per domain).
2. Resolve the target date from `$ARGUMENTS` (a day count like `3d`, a week count like `2w`, or an ISO date; empty means status mode).
3. Redistribute only the **unchecked** remaining work across the new timeframe in dependency order, then write it into the managed `REPLAN` block in the plan.

Do not tick any checkbox - completion is handled only by `/study-done`.
