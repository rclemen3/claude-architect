# CCA-F Checkpoint Log

A running record of study checkpoints against [`CCA-F-LEARNING-PLAN.md`](CCA-F-LEARNING-PLAN.md). This is calibration, not a braindump - the point is to see where understanding was thin and re-close it.

**Two layers, one file:**

1. **The status table below is the machine-readable ledger.** [`/check_prog`](.claude/commands/check_prog.md) reads it to know which topics already carry a checkpoint and what the verdict was, so it can show you the same standing as last time and never re-quizzes a **Pass** from cold. One row per plan topic.
2. **The dated entries further down are the narrative.** Each captures the quiz asked, the answers given, the verdict, and the correct framing.

**Verdicts:** `Pass` (defended it cleanly, would survive the exam), `Partial` (right idea, a piece missing), `Miss` (did not hold up), `Pending` (not yet checkpointed).

**A `Pass` ticks the plan.** When a checkpoint passes, [`/check_prog`](.claude/commands/check_prog.md) enforces the full [Definition of Done](docs/CCA-F-DEFINITION-OF-DONE.md) and **ticks the matching box** in `CCA-F-LEARNING-PLAN.md` in the same run - the **Plan ticked?** column records that it did. A `Partial` or `Miss` is logged here but ticks nothing, so the standing stays visible until you re-close it.

## Checkpoint status

| Topic | Last checkpoint | Verdict | Plan ticked? | Notes |
|---|---|---|---|---|
| 1.1 The Messages API primitive | 2026-08-14 | Pass | Yes | Re-quiz cleared - behavior-steering (system prompt) vs mechanical output bounds (`stop_sequences` / `max_tokens`) held cold. |
| 1.2 The agentic loop | 2026-08-14 | Pass | Yes | Re-quiz cleared - `stop_reason` state machine and the `tool_use` / `tool_result` `tool_use_id` contract both defended. |

---

## 2026-08-13 - Day 1 checkpoint (Topics 1.1 + 1.2)

**Scope claimed:** worked through the prerequisites up to and including `segment-1`. Orientation docs (`EXAM-STUDY-PATH.md`, `CERT-PROGRAM-BRIEFING.md`, `COURSE-FLOW.md`) skipped - left unticked.

**Verdict:** Not ticked. Notebooks ran, but the agentic-loop mechanics (Q2, Q4) did not hold up under questioning. **A green cell is not done - being able to explain it is.**

### Q1 - What makes the agentic loop terminate?
- **Answer given:** "stop_reason"
- **Verdict:** Partial. Named the field to branch on, not the two positions.
- **Correct framing:** The loop **continues while `stop_reason == "tool_use"`** (Claude is requesting a tool) and **terminates when `stop_reason == "end_turn"`** (Claude has a final answer).

### Q2 - Shape of a tool call, and what you send back
- **Answer given:** "to use a tool, need to specify the tool name"
- **Verdict:** Miss. Described tool *definition*, not the runtime round-trip.
- **Correct framing:**
  - When `stop_reason` is `tool_use`, Claude returns a **`tool_use` block** carrying an `id`, a `name`, and an `input` (the arguments).
  - You run the tool, then reply on the next turn with a **`tool_result` block** whose `tool_use_id` matches that `id`.
  - That id-matching pair **is** the loop's contract. Miss the `tool_use_id` and Claude cannot tell which call you answered.

### Q3 - System prompt vs. output control (`stop_sequences` / `max_tokens`)
- **Answer given:** "system prompt is persona, rule and tone"
- **Verdict:** Half. System-prompt half correct; the contrast was missing.
- **Correct framing:** A **system prompt** steers *behavior* (persona, rules, tone). `stop_sequences` and `max_tokens` do not shape behavior - they **mechanically bound the output** (stop when this string appears / cut off after N tokens). Behavior-steering vs. hard output limits are different levers.

### Q4 - What segment-1 demonstrates that a bare `messages.create()` does not
- **Answer given:** "messages.create() needs parameters such as model, max_token and message at least"
- **Verdict:** Miss. Described the *parameters* of the call, not what the notebook adds.
- **Correct framing:** The customer-support notebook wraps `messages.create()` in the **agentic loop** - `tools=` attached, the `stop_reason` branch, a dispatcher that runs the requested tool, and the `tool_result` sent back - so Claude can take *actions*, not just emit text.

### Remediation
- Re-walk **Part 1** of `notebooks/cca-f-exam-mastery.ipynb` (TS1.1-TS1.7).
- Skim the loop section of [`docs/domain-1-agentic.md`](docs/domain-1-agentic.md).
- Re-quiz on Q1 and Q2 cold before ticking Topic 1.1 and Topic 1.2.

---

## 2026-08-14 - Day 1 re-checkpoint (Topics 1.1 + 1.2) - both ticked

**Scope:** cold re-quiz of the two open Day 1 topics after the 2026-08-13 misses. Objective criteria confirmed by the learner - the Topic 1.1 prerequisite notebooks and `segment-0` + `segment-1` all ran green, and the `stop_reason` flip in segment-1 was read, not just executed.

**Verdict:** Pass on both. Topics 1.1 and 1.2 ticked in the plan.

### Q1 - System prompt vs. `stop_sequences` / `max_tokens` (Topic 1.1)
- **Answer given:** the system prompt defines **how Claude thinks** (persona, expertise, reasoning, approach) - instructional and behavioral; `stop_sequences` and `max_tokens` are **mechanical output constraints** that bound *when* and *how much*, not how Claude generates.
- **Verdict:** Pass. This is the exact contrast that was missing on 2026-08-13.
- **Sharpening:** `stop_sequences` is the **stopping-trigger** lever (halt when a string appears); `max_tokens` is the **length-ceiling** lever. Both mechanical, two different bounds.

### Q2 - What drives the agentic loop (Topic 1.2)
- **Answer given:** the loop continues or stops on `stop_reason`; `end_turn` = Claude finished and awaits the user, `tool_use` = Claude requested a tool and the caller must run it and return the result; `stop_reason` is always present in the response.
- **Verdict:** Pass. Named the field and both values, and the mechanic - **continue while `tool_use`, terminate on `end_turn`**.

### Q3 - The tool round-trip (Topic 1.2)
- **Answer given:** Claude sends a **`tool_use`** content block (tool name + input arguments); the caller replies with a **`tool_result`** content block carrying the output; **`tool_use_id`** ties them and the caller must echo the `id`.
- **Verdict:** Pass. This is the id-matching contract that was the Q2 miss on 2026-08-13.

**Forward note:** the loop half of Topic 1.2 is solid. Topic 1.3 and the Day 1 gate lean on the other domain-1 concepts - **PreToolUse hook determinism** and **coordinator-subagent isolation**. Those were not part of this re-quiz; keep them sharp going into `hooks-example.py` and `coordinator-subagent-sketch.py`.
