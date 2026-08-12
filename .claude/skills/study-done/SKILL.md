---
name: study-done
description: Mark CCA-F study progress complete in CCA-F-LEARNING-PLAN.md. Invoke as /study-done when the learner finishes a topic, notebook, domain, day gate, or the readiness gate and wants the checklist updated. Verifies the Definition of Done before ticking any box, cascades to domain/gate rows when their sub-items are all complete, and reports updated progress. Also handles a status-only request and un-ticking a box.
---

# study-done

Update the study checklist in [`CCA-F-LEARNING-PLAN.md`](../../../CCA-F-LEARNING-PLAN.md) as the learner completes material, but **only after confirming the Definition of Done is met**. The rulebook is [`docs/CCA-F-DEFINITION-OF-DONE.md`](../../../docs/CCA-F-DEFINITION-OF-DONE.md) - load it every run; it is the source of truth for what "done" means.

Guiding principle from the DoD: **a green cell is not done - being able to explain it is.** Never tick a box the learner cannot defend.

## Inputs

The argument after `/study-done` names what to mark. Accept any of:
- A topic id or notebook: `1.2`, `Segment 1`, `Topic 3.1`, `exam-mastery Part 2`, `prerequisites`
- A domain: `D2`, `domain 4`
- A gate: `Day 3 gate`, `readiness`
- Nothing at all -> run in **status mode** (see below)
- `status` -> status mode explicitly
- `undo <item>` -> un-tick (revert `[x]` to `[ ]`) after confirming

## Procedure

1. **Read the plan and the DoD.** Read [`CCA-F-LEARNING-PLAN.md`](../../../CCA-F-LEARNING-PLAN.md) and [`docs/CCA-F-DEFINITION-OF-DONE.md`](../../../docs/CCA-F-DEFINITION-OF-DONE.md) in full.

2. **Resolve the target.** Map the argument to specific `[ ]` line(s) in the plan. If the argument is ambiguous or matches nothing, list the closest unchecked items and ask which they mean. Do not guess.

3. **Apply the Definition of Done for that item type.** Look up the matching row in the DoD table and check each criterion:
   - **Objective criteria** (cells ran, teardown cells ran, sidecar bound its port): state them and ask the learner to confirm.
   - **Demonstrable criteria** (explain out loud, restate a concept, why each distractor fails, the Day gate questions): actually **quiz the learner** with one or two targeted questions drawn from that day's gate or the relevant task statement. Judge the answer. This is the part that matters - do not wave it through.
   - If any criterion is unmet, **do not tick.** Tell the learner exactly what is missing and stop.

4. **Tick on pass.** Use Edit to change the matched `[ ]` to `[x]` on those exact lines. When editing, obey the repo voice rules in `CLAUDE.md` (the plan is a committed repo doc): no em dashes, hyphen-with-spaces, Azure-first if cloud comes up, bold key terms.

5. **Cascade.** After ticking, check whether a parent now qualifies:
   - If every sub-item of a Topic or Day is now `[x]`, note it.
   - If a domain's notebook + scaffold + exam-mastery Part + 100% practice pass are all `[x]`, run the DoD for the **achievement-tracker row** for that domain and, on pass, tick it too.
   - If every achievement row and every readiness criterion is `[x]`, run the **Exam-readiness gate** DoD and, on pass, tick those rows.
   Never auto-tick a parent whose own DoD is not met - a domain row has its own bar (the 100% practice pass), not just "sub-items touched."

6. **Report progress.** End with a short summary:
   - Items ticked this run.
   - Overall completion: `N/M` checkboxes.
   - Per-domain completion (D1-D5) as a quick line.
   - The single **next recommended item** from the plan's order.

## Status mode

With no argument or `status`: change nothing. Read the plan and print overall completion, per-domain completion, which Day the learner is on, and the next unchecked item. Offer to mark the current in-progress topic done if its DoD holds.

## Undo mode

With `undo <item>`: confirm the learner really wants to revert (studying rarely un-happens; this is for miscategorized ticks), then flip `[x]` back to `[ ]` and re-run the cascade so any parent that no longer qualifies is also reverted.

## Boundaries

- **You cannot observe that a notebook ran.** You did not watch them study. Confirm objective criteria by asking; confirm learning by quizzing. Both, every time.
- **Do not lower the bar to be helpful.** A learner who wants a box ticked without meeting the DoD is asking you to help them fail the real exam. Hold the line, kindly.
- Touch only `CCA-F-LEARNING-PLAN.md`. Do not edit the DoD or the notebooks from this skill.
