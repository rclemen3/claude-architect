---
name: replan
description: Re-fit the remaining CCA-F study work into a new number of days in CCA-F-LEARNING-PLAN.md. Invoke as /replan when the learner has fallen behind, gained time, or wants to compress or stretch the schedule. Takes a day count (e.g. /replan 3d), reads current progress, computes a target completion date, and rewrites a managed schedule block that redistributes only the unchecked work across the new timeframe in dependency order. Never ticks boxes - that is /check_prog's job.
---

# replan

Re-pace the study schedule in [`CCA-F-LEARNING-PLAN.md`](../../../CCA-F-LEARNING-PLAN.md) when the learner's real timeline no longer matches the authored **Day 1-5** plan. The plan is written for 5 days at up to 8 hours/day; life rarely cooperates. This skill takes the days the learner actually has left, looks at what they have **already completed**, and lays out a fresh day-by-day route through everything still unchecked.

**Boundary that matters:** this skill **reschedules**, it does not grade. It never flips a `[ ]` to `[x]`. Marking work done - with the Definition of Done enforced - is [`/check_prog`](../../commands/check_prog.md)'s job. Keep the two separate.

## Inputs

The argument after `/replan` is how many days remain:
- `3d`, `3`, `3 days` -> three days from today
- `2w` -> two weeks (14 days)
- An ISO date (`2026-08-20`) -> target that calendar date directly
- Nothing at all -> **status mode**: report current progress, the remaining workload in hours, and the currently stamped target date if one exists, then ask how many days they want before rewriting anything.

Validate the count is at least 1. If it parses to zero or a past date, stop and ask again.

## What counts as "remaining work"

Only reschedule genuine **study items** that are still `[ ]`:
- One-time setup items (if any are unchecked)
- Every `Topic` sub-item under Days 1-5

Do **not** place these into the schedule as movable work - they are consequences that attach to wherever their inputs land:
- **Day gates** - attach each to the last day carrying that day's topics
- **Achievement-tracker rows** and the **Exam-readiness gate** - these flip only when their inputs are done; leave them where they are and reference them at the end of the new schedule
- The **7-day appendix** - reference material, never rewritten

## Procedure

1. **Read the plan.** Read [`CCA-F-LEARNING-PLAN.md`](../../../CCA-F-LEARNING-PLAN.md) in full. Skim [`docs/CCA-F-DEFINITION-OF-DONE.md`](../../../docs/CCA-F-DEFINITION-OF-DONE.md) only so your day-gate framing matches the real bar; do not edit it.

2. **Measure progress.** Count `[x]` vs `[ ]` overall and per domain (D1-D5). This is the "condition" the replan responds to - the schedule you emit contains **only the unchecked items**.

3. **Estimate the remaining hours.** Sum the `(≈X hrs)` / `(≈X min)` annotations on the unchecked topics. Where a sub-item carries no estimate of its own, inherit its Topic header's estimate, split evenly across that Topic's unchecked sub-items. Report the total.

4. **Resolve the target date.** Use the current date from the session context (do not guess a date). Compute `target = today + N days` (or use the ISO date if given). State both today and the target explicitly.

5. **Check the daily load is sane.** `hours_per_day = remaining_hours / N`.
   - The repo's stated ceiling is **8 hours/day**. If `hours_per_day` exceeds 8, warn plainly, then offer two moves before writing:
     - **Add days** - suggest the smallest N that lands under 8 hrs/day.
     - **Triage** - the plan flags the `notebooks/06-managed-agents/` notebooks as **billable/beta** and Segment 2.5 as an all-domain **depth pass**. These are the first legitimate trim candidates when time is short. Never propose cutting a domain's core notebook, its scaffold, its exam-mastery Part, or its practice questions - those are load-bearing for a one-attempt exam.
   - If `hours_per_day` is very low (a generous stretch), say so and keep the ordering relaxed.

6. **Redistribute.** Bucket the unchecked Topics into N days:
   - **Preserve dependency order.** The plan is already ordered D1 -> D5 and that order respects prerequisites (the Messages API primitive before the loop, tools before MCP, and so on). Do not reorder across that grain.
   - **Balance toward `hours_per_day`.** Fill each day close to the target before moving on.
   - **Keep a Topic whole** on one day unless a single Topic alone exceeds the daily budget - only then split it, and split at sub-item boundaries.
   - **Weight the front.** When something has to give, protect the heaviest unlearned domains first (D1 27%, then D3/D4 at 20%, D2 18%, D5 15%).
   - Keep the coverage-complete pass, the capstone, and the 60-question bank in the **final** day or two.

7. **Write the managed block.** Insert or replace a single block delimited by the markers below, placed **after the intro/ground-rule section and before the first `## ` day section**. Re-running `/replan` replaces this block in place - it is idempotent by design. Never scatter schedule edits through the day sections; the checkboxes there stay untouched so `/check_prog` keeps working against them.

   ```
   <!-- REPLAN:START -->
   ## Replanned schedule (target: <YYYY-MM-DD>, <N> days, replanned <today>)

   <one line: X of Y items done; ~H hours of study remaining; ~h hrs/day over N days>
   <the 8 hrs/day warning and triage note, only if it applies>

   ### Replan Day 1 - <YYYY-MM-DD> (~h hrs)
   - [`Topic x.y - <name>`](#anchor) - <link the same items the day section links>
   - ...
   - **Gate:** <that day's Day gate, if its topics all land here>

   ### Replan Day 2 - <YYYY-MM-DD> (~h hrs)
   - ...

   > This schedule reschedules only unchecked work. Tick items with `/check_prog`; re-run `/replan` if your timeline shifts again.
   <!-- REPLAN:END -->
   ```

   Follow the repo voice rules from `CLAUDE.md` when writing: **no em dashes** (hyphen-with-spaces), no "ask" as a noun, **Azure-first** if cloud comes up, **bold key terms**, no glazing openers. Link each item to the same target its original day-section line links to, so the learner can jump straight to the work.

8. **Report.** After writing, print a short summary: today and target date, N days, items remaining, hours/day, whether a warning fired, and the single next item to start on Replan Day 1.

## Status mode

With no argument or `status`: change nothing. Read the plan, print `[x]`/`[ ]` overall and per domain, the remaining hours, and the target date if a managed block already carries one. Then ask how many days the learner wants before you rewrite.

## Boundaries

- **Never tick or un-tick a box.** Scheduling is not grading. Route all completion through `/check_prog`.
- **Touch only the managed block** between the `REPLAN` markers (plus, on first use, inserting that block). Leave the authored day sections, the achievement tracker, the readiness gate, the appendix, and the DoD alone.
- **Do not invent estimates.** If a topic has no hours annotation and no header to inherit from, say so rather than fabricating a number.
- **Do not lower the exam bar to fit the calendar.** If the days genuinely cannot hold the core work, say that honestly and recommend more days rather than quietly dropping load-bearing material.
