---
description: Check CCA-F study progress, run an optional calibration quiz, and tick the plan box on a pass (enforces the Definition of Done). Also handles status-only and undo.
argument-hint: "[topic id | domain | gate | undo <item> | status | (empty)]"
---

The single tool for grading `CCA-F-LEARNING-PLAN.md`. It reports progress, **quizzes you**, and on a pass **ticks the plan box for you** after enforcing the [Definition of Done](../../docs/CCA-F-DEFINITION-OF-DONE.md). It also maintains the checkpoint log. `/replan` only reschedules; it never grades. There is no separate ticking command.

Guiding principle from the DoD: **a green cell is not done - being able to explain it is.** Never tick a box the learner cannot defend.

## Modes (from `$ARGUMENTS`)

- **empty** or `status` -> **status mode**: report, change nothing, offer a quiz.
- a **topic / notebook / domain / gate** (`1.2`, `Segment 1`, `D2`, `Day 3 gate`, `readiness`) -> target that item.
- `undo <item>` -> **undo mode**: revert a tick after confirming.

## Step 1 - Read everything

Read in full:
- [`CCA-F-LEARNING-PLAN.md`](../../CCA-F-LEARNING-PLAN.md) - the checklist.
- [`CCA-F-CHECKPOINT-LOG.md`](../../CCA-F-CHECKPOINT-LOG.md) - the **Checkpoint status** table (the ledger) and the dated narrative entries.
- [`docs/CCA-F-DEFINITION-OF-DONE.md`](../../docs/CCA-F-DEFINITION-OF-DONE.md) - the bar you enforce before ticking.

If the mode is `undo`, skip to **Undo mode** below.

## Step 2 - Report status (read-only)

Print, changing nothing:
- **Plan completion:** overall `N/M` checkboxes; per-domain (D1-D5) as a quick line; which Day the learner is on; the single **next unchecked** item in plan order.
- **Checkpoint standing** from the status table: for each topic with a row, `Topic - Verdict (date)`. Flag any **Partial** / **Miss** as still open and any unchecked topic that is **Pending**.

## Step 3 - Resolve the target and offer the quiz

- If `$ARGUMENTS` names a topic, domain, or gate, that is the target. Otherwise the target is the **current in-progress topic**, or if none, the **next unchecked** item.
- If the target is already `[x]` in the plan, say so; a re-quiz is optional.
- **Offer** to run a checkpoint quiz on the target and **wait for consent**. If the learner declines, stop after the report - write nothing to any file.

## Step 4 - Apply the Definition of Done

On a yes, look up the matching row in the DoD table and check each criterion:
- **Objective criteria** (cells ran, teardown/archive cells ran, sidecar bound its port): state them and ask the learner to confirm.
- **Demonstrable criteria** (explain out loud, restate a concept, why each distractor fails, the Day gate questions): **quiz the learner** with 2 to 4 targeted questions drawn from that day's gate or the relevant task statements (TSx.y). Judge each answer honestly - a "mostly" is a `Partial`, not a `Pass`. Do not wave anything through.

Verdict for the topic: `Pass` (every criterion met, defended cleanly), `Partial` (right idea, a piece missing), or `Miss` (did not hold up).

## Step 5 - Log the checkpoint (whenever a quiz ran)

Write to `CCA-F-CHECKPOINT-LOG.md` (obey repo voice rules: no em dashes, ` - ` instead, Azure-first if cloud comes up, bold key terms):

1. **Append a dated narrative entry** below the existing ones: `## <today's date> - <topic(s)> checkpoint` (use today's date from context), then **Scope**, **Verdict**, one block per question (**answer given**, **verdict**, **correct framing**), and a **Remediation** list if the verdict is not `Pass`.
2. **Update the status-table row** for the quizzed topic(s) (insert one if absent): **Last checkpoint** = today, **Verdict** = the judged result, a one-line **Notes**, and **Plan ticked?** = `Yes` only if Step 6 ticks it this run, else `No`.

## Step 6 - Tick the plan on a pass, then cascade

Only if the verdict is **Pass**:
1. Use Edit to change the matched `[ ]` to `[x]` on those exact lines in `CCA-F-LEARNING-PLAN.md` (same voice rules).
2. **Cascade** to parents whose own DoD now holds:
   - Every sub-item of a Topic or Day now `[x]` -> note it.
   - A domain's notebook + scaffold + exam-mastery Part + 100% practice pass all `[x]` -> run the DoD for that **achievement-tracker row** and, on pass, tick it.
   - Every achievement row and every readiness criterion `[x]` -> run the **Exam-readiness gate** DoD and, on pass, tick those rows.
   Never auto-tick a parent whose own bar is unmet - a domain row needs its 100% practice pass, not just "sub-items touched."

If the verdict is **Partial** or **Miss**, do **not** tick. Tell the learner exactly what is missing (the checkpoint entry from Step 5 already records it) and stop.

## Step 7 - Report the outcome

End with a short summary: items ticked this run, overall `N/M`, per-domain completion, and the single **next recommended item** in plan order.

## Undo mode

With `undo <item>`: confirm the learner really wants to revert (studying rarely un-happens; this is for miscategorized ticks), then flip `[x]` back to `[ ]` and re-run the cascade so any parent that no longer qualifies also reverts. Update that topic's checkpoint-table **Plan ticked?** back to `No`.

## Boundaries

- **This command is the only grader.** `/replan` reschedules and never ticks; ticking lives here.
- **Never tick without a quiz pass and a met DoD.** Objective criteria confirmed *and* demonstrable criteria quizzed - both, every time.
- **A no-consent run changes nothing.** A bare status report leaves the plan and the checkpoint log untouched.
- **Hold the bar.** A learner who wants a `Pass` they cannot defend is being helped to fail the real exam. Record what they actually demonstrated.
