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
- **Demonstrable criteria** (explain out loud, restate a concept, why each distractor fails, the Day gate questions): **quiz the learner** with 2 to 4 targeted questions. Judge each answer honestly - a wrong pick is a `Miss` for that question; do not wave anything through.

**Quiz format - single-answer multiple choice, exactly like the real exam.** Every question is one stem plus **four options labeled A, B, C, D, with exactly one correct answer**. The learner replies with a single letter. Do **not** ask free-writing, "explain why", or short-answer questions - the real CCA-F exam is select-one-of-four, so the calibration quiz must match it. Present all options; wait for the learner's letter before revealing the answer.

- **Write real exam distractors.** All three wrong options must be plausible to someone who half-understands the concept - a common misconception, a nearby-but-wrong primitive, a right idea applied to the wrong situation. No throwaway or obviously-absurd options. The learner should have to actually know the concept to rule them out.
- **After the learner picks, you supply the distractor rationale.** The learner only selects a letter; you then state which is correct and, in the Step 5 log, explain why each of the other three is wrong. Do not require the learner to write out reasoning - grade the letter.

**Quiz subject - write CCA-F exam questions, not repo-recall questions.** The target topic and its task statements (TSx.y) fix the **subject** a question probes, but the question itself must look like one the learner will meet on the real CCA-F exam, not a quiz about this repo:

- **Test the domain concept generally, not the demo.** Ask what a control does, when to reach for it, and how it behaves in production - not "what did that notebook cell print" or "which file holds the MCP config." A learner who memorized this repo but does not understand the concept should fail; a learner who understands the concept but never saw this repo should pass.
- **Prefer scenario-framed, application-level questions.** Put Claude in a realistic situation (a support agent looping, an extractor failing validation, a token budget blowing out, a secret needing rotation) and make the learner choose the right primitive, parameter, or design from the four options. Favor the exam's own vocabulary (agentic loop, `stop_reason`, `tool_choice`, prompt caching, context window, structured errors) over repo-local names.
- **Stay off repo trivia.** No questions about file paths, script flags, sidecar ports, kernel names, this repo's model policy, or which segment covers which domain. Those are logistics, not exam objectives. The public CCA-F exam domains and task statements are the syllabus; the repo is only how the learner studied them.

Each question is graded on the letter alone: the learner's pick is either **correct** or **wrong**. Verdict for the topic: `Pass` (objective criteria met and every quiz question answered correctly), `Partial` (most correct, one slip on a non-core question), or `Miss` (a core question wrong, or several wrong).

## Step 5 - Log the checkpoint (whenever a quiz ran)

Write to `CCA-F-CHECKPOINT-LOG.md` (obey repo voice rules: no em dashes, ` - ` instead, Azure-first if cloud comes up, bold key terms):

1. **Append a dated narrative entry** below the existing ones: `## <today's date> - <topic(s)> checkpoint` (use today's date from context), then **Scope**, **Verdict**, one block per question, and a **Remediation** list if the verdict is not `Pass`. Record the full question with its four options so the log reads as a study resource cold. Each question block carries, in this order:
   - **Question and options** - the stem and all four labeled options A-D.
   - **Answer given** - the letter the learner picked, and the correct letter (e.g. `Picked B; correct is C`).
   - **Verdict** - `Correct` or `Wrong` and a one-line judgment.
   - **Explanation** - a full, exam-quality explanation that names the correct option **and states why each of the other three distractors is wrong**, written **for every question regardless of verdict** so the log reads as a study resource cold. State the concept as the CCA-F exam tests it, not as this repo demonstrates it.

   This **Explanation** line is not optional and is not reserved for wrong answers - a clean `Pass` still gets one. (Older entries may also carry a `Correct framing` or `Sharpening` line; those are equivalent and may stay, but every new block uses **Explanation**.)
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
