# CCA-F Definition of Done

This is **the rule** for [`CCA-F-LEARNING-PLAN.md`](../CCA-F-LEARNING-PLAN.md): what has to be true before a checkbox flips from `[ ]` to `[x]`. The [`/check_prog`](../.claude/commands/check_prog.md) command enforces it. Read it as the standard you hold yourself to even when ticking a box by hand.

## The one principle everything else follows from

**A green cell is not done. Being able to explain it is.**

This mirrors the repo's own smoke-test rule (`CLAUDE.md`): *a passing exit code is not enough* - you read the output and confirm it matches the claim. Studying is the same. "The notebook ran" proves the environment works, not that you learned anything. An item is **done** when you can demonstrate the skill out loud, from memory, in your own words. The exam gives you **one attempt**; calibrate honestly here so you don't discover the gap there.

Corollary: **never tick a box you cannot defend.** If asked "why is this done?" and the honest answer is "the cells were green," it is not done.

## Definition of Done by item type

| Item type (where it appears in the plan) | Done when ALL of these are true |
|---|---|
| **One-time setup** | Environment bootstraps; `notebooks/.env` carries `ANTHROPIC_API_KEY`; the three orientation docs are read, not skimmed. |
| **A notebook worked** (Segments 0-4, `00-prerequisites/`) | Every code cell ran in a real kernel with **no errors**; you **read the output** of any cell asserting an observable behavior (`stop_reason` flips, cache counters, `tool_use` blocks, structured errors) and confirmed it matches the markdown claim; you can state in one sentence what the notebook demonstrates. |
| **A billable notebook** (`06-managed-agents/`) | All of the above, **plus** the teardown/archive cells ran so nothing is left provisioned. Verify the archive, do not assume it. |
| **A domain scaffold read** (`docs/domain-N.md`) | Read in full; you can restate each **Core concept** H3 in your own words without looking at the page. |
| **An exam-mastery Part walked** (Parts 1-6) | Every cell in that Part ran green (the live MCP `list_tools` cell may be skipped under headless `nbconvert` - that skip is correct); you can name the **task statements** (TSx.y) the Part covers. |
| **MCP hands-on / sidecars** | The sidecar actually **bound its port** (probe it - do not trust a launcher's exit code); you invoked at least one tool through the Inspector or the CLI REPL and saw a real result come back. |
| **Practice questions, per domain** | You answered **every** question in that domain; you scored **100% on a clean re-pass**; for each answer you got wrong the first time, you explained **out loud why each distractor fails**, not just which option is right. |
| **A Day gate** | You answered that day's gate questions **out loud, without notes**. A "mostly" is a no. |
| **An achievement-tracker domain row** | That domain's notebook work, scaffold, exam-mastery Part, **and** 100% practice-question pass are each independently done per the rows above. |
| **The Exam-readiness gate** | Every one of its checkboxes is genuinely true: all five domain rows done; full exam-mastery pass with no surprises; **60-question bank at 90%+ overall and no single domain below 80%**; you can talk through all six scenario families and name their domains; **Anthropic's official Practice Exam taken and passed comfortably**; the week-before punchlist reviewed. |

## What does not count as done

- The cell ran green but you did not read what it printed.
- You read the scaffold but could not restate a concept a day later.
- You "got the gist" of the practice questions but did not hit 100% on a clean pass.
- You recognize the right answer but cannot say why the wrong ones are wrong.
- A billable notebook ran but you skipped the teardown cells.

Any of these means the box stays `[ ]`.
