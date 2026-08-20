# CCA-F Checkpoint Log

A running record of study checkpoints against [`CCA-F-LEARNING-PLAN.md`](CCA-F-LEARNING-PLAN.md). This is calibration, not a braindump - the point is to see where understanding was thin and re-close it.

**Two layers, one file:**

1. **The status table below is the machine-readable ledger.** [`/check_prog`](.claude/commands/check_prog.md) reads it to know which topics already carry a checkpoint and what the verdict was, so it can show you the same standing as last time and never re-quizzes a **Pass** from cold. One row per plan topic.
2. **The dated entries further down are the narrative.** Each captures the quiz asked, the answers given, the verdict, and - for every question - an **explanation** of the correct answer, so the log doubles as a study resource you can re-read cold.

**Verdicts:** `Pass` (defended it cleanly, would survive the exam), `Partial` (right idea, a piece missing), `Miss` (did not hold up), `Pending` (not yet checkpointed).

**A `Pass` ticks the plan.** When a checkpoint passes, [`/check_prog`](.claude/commands/check_prog.md) enforces the full [Definition of Done](docs/CCA-F-DEFINITION-OF-DONE.md) and **ticks the matching box** in `CCA-F-LEARNING-PLAN.md` in the same run - the **Plan ticked?** column records that it did. A `Partial` or `Miss` is logged here but ticks nothing, so the standing stays visible until you re-close it.

## Checkpoint status

| Topic | Last checkpoint | Verdict | Plan ticked? | Notes |
|---|---|---|---|---|
| 1.1 The Messages API primitive | 2026-08-14 | Pass | Yes | Re-quiz cleared - behavior-steering (system prompt) vs mechanical output bounds (`stop_sequences` / `max_tokens`) held cold. |
| 1.2 The agentic loop | 2026-08-14 | Pass | Yes | Re-quiz cleared - `stop_reason` state machine and the `tool_use` / `tool_result` `tool_use_id` contract both defended. |
| 1.3 D1 exam mapping | 2026-08-14 | Pass | Yes | The two forward-noted concepts held cold - **PreToolUse determinism** (runtime enforces, not the model) and **coordinator-subagent isolation** (prevents context contamination). Part 1 coverage and the PostToolUse lifecycle both defended. |
| 2.1 Managed agents (read-only) | 2026-08-17 | Pass | Yes | Objective bar confirmed (managed 01/02 read); the managed-vs-hand-rolled loop concept already Passed. Ticked. |
| 2.2 Tool design | 2026-08-17 | Pass | Yes | `tool_choice` re-close clean - four modes mapped correctly, `disable_parallel_tool_use` direction right, and the **forcing-a-tool-suppresses-reasoning** tradeoff (Q2) nailed with both real remedies. Ticked. |
| 2.3 MCP hands-on | 2026-08-17 | Pass (exception) | Yes (exception) | Concepts Passed 08-17 (transports Q3, `list_tools` Q4). **Hands-on bar NOT met** - no sidecar bound, no live tool invoked (D = No). Third box **ticked by explicit learner exception**, no quiz run. Topic shown complete but the hands-on round-trip was never done. |
| 3.1 Configuration hierarchy | 2026-08-18 | Pass | Yes | Re-quiz cleared. Full precedence order **managed > project > user** stated with the project-over-user rung (Q1); **deterministic enforcement** now correct - a hook acting **before tool execution**, enforced by runtime code not the model (Q2). Ticked. |
| 3.2 D3 exam mapping | 2026-08-18 | Pass | Yes | Re-quiz cleared. "Which is NOT true" on skills (Q4): correct false pick **and** the *why* for every option stated. Ticked. |
| 3.3 Workflows: CI/CD + headless | 2026-08-18 | Pass | Yes | Re-quiz cleared. Headless for unattended CI/CD; plan mode's defining property now named - **review before any file is written to disk** (Q3). Ticked. |
| 4.1 Structured output | 2026-08-19 | Pass | Yes | Clean first-pass. **Structure vs semantic validity** (Q1) and the two prompt-only failure modes (Q2) both defended; `cache_control` win + byte-stable prefix (Q3), sharpened with the Haiku 4.5 4096-token floor. Ticked. |
| 4.2 Context management & reliability | 2026-08-19 | Pass | Yes | Clean first-pass. Two context-preservation moves with tradeoffs (Q4); escalation triggers with **uncertainty operationalized** (Q5); provenance = source id + evidence location + audit trail (Q6). Ticked. |
| 4.3 Control surfaces depth pass | 2026-08-19 | Pass | Yes | Clean first-pass. Four levers correctly split **tool behavior vs output mechanics** (Q7); `list_tools` runtime discovery vs hardcoded redeploy (Q8), bonus `max_tokens` stop_reason correct. Ticked. |

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
- **Explanation:** The **system prompt** is the behavioral layer - it sets who Claude is and how it reasons, and it shapes the *content* of every response. `stop_sequences` and `max_tokens` never touch content; they are **runtime output bounds**. `max_tokens` caps generation length (a response cut off here returns `stop_reason: max_tokens`); `stop_sequences` halts the moment a named string is emitted (returning `stop_reason: stop_sequence`). Reach for the system prompt to change *what* Claude says, and for the two bounds to guarantee *when it stops*.
- **Sharpening:** `stop_sequences` is the **stopping-trigger** lever (halt when a string appears); `max_tokens` is the **length-ceiling** lever. Both mechanical, two different bounds.

### Q2 - What drives the agentic loop (Topic 1.2)
- **Answer given:** the loop continues or stops on `stop_reason`; `end_turn` = Claude finished and awaits the user, `tool_use` = Claude requested a tool and the caller must run it and return the result; `stop_reason` is always present in the response.
- **Verdict:** Pass. Named the field and both values, and the mechanic - **continue while `tool_use`, terminate on `end_turn`**.
- **Explanation:** Every Messages API response carries a **`stop_reason`**, and the agentic loop branches on it. `tool_use` means Claude paused to call a tool and the caller must run it and feed the result back; `end_turn` means Claude produced its final answer and the loop exits. The other values (`max_tokens`, `stop_sequence`) signal a mechanical cutoff, not task completion. The loop is therefore `while stop_reason == "tool_use": run tools, send results back`.

### Q3 - The tool round-trip (Topic 1.2)
- **Answer given:** Claude sends a **`tool_use`** content block (tool name + input arguments); the caller replies with a **`tool_result`** content block carrying the output; **`tool_use_id`** ties them and the caller must echo the `id`.
- **Verdict:** Pass. This is the id-matching contract that was the Q2 miss on 2026-08-13.
- **Explanation:** The round-trip is a pair of content blocks bound by an id. Claude emits a **`tool_use`** block (`id`, `name`, `input`); the caller runs the named tool and replies with a **`tool_result`** block whose **`tool_use_id`** echoes that `id`. The id is what lets Claude match a result to the call that produced it - essential once several tools run in one turn. An error is reported *inside* the result block (`is_error: true` on the protocol block, an `isError` payload the model reads), never by raising an exception.

**Forward note:** the loop half of Topic 1.2 is solid. Topic 1.3 and the Day 1 gate lean on the other domain-1 concepts - **PreToolUse hook determinism** and **coordinator-subagent isolation**. Those were not part of this re-quiz; keep them sharp going into `hooks-example.py` and `coordinator-subagent-sketch.py`.

---

## 2026-08-14 - Topic 1.3 checkpoint (D1 exam mapping) - ticked

**Scope:** the two open Topic 1.3 items - **Part 1** of `cca-f-exam-mastery.ipynb` (TS1.1-TS1.7) and reading `hooks-example.py` + `coordinator-subagent-sketch.py`. Objective bar confirmed by the learner: every Part 1 cell ran green, both `.py` files read. Quiz targeted the two concepts the 2026-08-14 forward note flagged as un-quizzed.

**Verdict:** Pass on all four. Topic 1.3 ticked in the plan. This completes **all of Day 1**.

### Q1 - PreToolUse hook determinism
- **Answer given:** `CLAUDE.md` is enforced only by the model as an instruction, so it is a request Claude may follow but not a hard guarantee; a **PreToolUse hook** is enforced by runtime code before the tool executes, so the system - not the model - blocks dangerous commands.
- **Verdict:** Pass. Named both enforcers and the moment of enforcement. Deterministic vs. probabilistic, correctly located.
- **Explanation:** `CLAUDE.md` and system-prompt rules are **probabilistic guardrails** - the model usually honors them, but nothing outside the model enforces them. A **PreToolUse hook** is runtime code that runs *before* the tool executes and can block or rewrite the call deterministically. When a guarantee must hold every time (block `rm -rf`, forbid a path, require approval), it belongs in a hook, not in an instruction the model may override.

### Q2 - Coordinator-subagent isolation
- **Answer given:** a subagent with its own context prevents **context contamination** - in one long conversation a failed reasoning branch, irrelevant tool calls, or hallucinated assumptions pollute the shared transcript and mislead the coordinator; isolation keeps errors local, preserves token budget, and returns only the distilled result.
- **Verdict:** Pass. Correct primary failure mode plus the right secondary wins. Ties to the notebook point that subagent context must be **explicitly passed in the prompt** because isolation means no automatic inheritance.
- **Explanation:** A subagent runs in its **own context window**, so its intermediate reasoning, failed branches, and tool chatter never enter the coordinator's transcript - only the distilled result returns. This prevents **context contamination** (noise misleading later reasoning) and conserves the coordinator's **token budget**. The tradeoff: isolation means no automatic inheritance, so any context the subagent needs must be **passed explicitly in its prompt**.

### Q3 - Part 1 coverage (TS1.1-TS1.7)
- **Answer given:** the full foundation of agentic architecture - the reasoning loop, stop conditions, tool calling and dispatch, `tool_use` / `tool_result` matching, pre/post hooks, session and state handling, orchestration patterns, and task decomposition.
- **Verdict:** Pass. Spanned Domain 1 end to end; named the concept territory of all seven task statements.
- **Explanation:** Domain 1 spans the full agentic architecture: the reasoning loop and its `stop_reason` stop conditions, tool calling and dispatch, the `tool_use` / `tool_result` matching contract, pre/post hooks, session and state handling, and multi-agent orchestration (coordinator-subagent, task decomposition). Being able to name each of these as a distinct capability - not recite the TSx.y numbers - is the coverage bar for TS1.1 through TS1.7.

### Q4 - Hook lifecycle applied
- **Answer given:** after a tool returns, the **PostToolUse** hook fires; a useful use case is logging and observability - tool name, input, output, latency, errors - for debugging, auditing, and spotting unreliable tools before their results continue through the loop.
- **Verdict:** Pass. Correct lifecycle position and a sharp, concrete use case.
- **Explanation:** Hooks fire at fixed lifecycle points: **PreToolUse** before a tool runs (validate, block, rewrite) and **PostToolUse** after it returns (observe, log, react). PostToolUse is the natural home for **observability** - recording tool name, input, output, latency, and errors - which surfaces unreliable tools before their output flows on through the loop. The key distinction: Pre gates the call, Post reacts to the result.

**Forward note:** Day 1 is complete and its gate is defended cold. Next is **Day 2, Topic 2.1** - managed agents (`06-managed-agents/01` and `02`), which is billable/beta and needs its teardown cells to run in the same sitting. The **D1 achievement-tracker row** is not yet ticked: it still needs managed 01/02, `domain-1` scaffold restatement, and a 100% pass on the 11 D1 practice questions.

---

## 2026-08-17 - Day 2 checkpoint (Topics 2.1 + 2.2 + 2.3) - nothing ticked

**Scope claimed:** all of Day 2 done, requested a quiz to tick every Topic 2 box. Calibration quiz run across the three topics. Objective bars (A-E) were not confirmed by the learner in this run.

**Verdict:** Nothing ticked. Q2 (`tool_choice`) was a **Miss**, which blocks Topic 2.2 on its own. Topics 2.1 and 2.3 also lack confirmed objective bars, and Topic 2.3's MCP concepts were not quizzed. **A green cell is not done - being able to explain it is.**

### Q1 - Three schema/description fixes for a mis-called tool (Topic 2.2)
- **Answer given:** regex pattern on `order_id` that rejects email-shaped strings; a field-level description at the point of use; remove `amount` from the schema so an un-declared field cannot be invented.
- **Verdict:** Pass. All three live in the schema/description layer, all distinct. Correctly treats **the tool description as the contract**.
- **Explanation:** When a tool is mis-called, the durable fix lives in the tool's **contract** - its JSON schema and descriptions - not in prompt patching. A `pattern` regex constrains a field to valid shapes (rejecting an email-shaped `order_id`); a field-level `description` disambiguates at the point of use; and removing an undeclared field stops the model inventing arguments the tool never accepts. Tightening the schema is how you make a tool self-documenting and hard to misuse.

### Q2 - Match `tool_choice` modes to guarantees (Topic 2.2)
- **Answer given:** (a) `{"type":"tool","name":...}`; (b) `{"type":"any"}`; (c) `{"type":"auto","disable_parallel_tool_use": False}`.
- **Verdict:** Miss. (a) correct. (b) and (c) swapped: "decide whether *any* tool is needed" is **`auto`** (tool optional), and "must call a tool, model's pick, no parallel" is **`any`** with **`disable_parallel_tool_use: true`** (the value given was `false`, which permits parallel). The guarantee each mode gives was also not stated.
- **Correct framing:** **`auto`** = tool optional, Claude may answer with text; **`any`** = a tool call is mandatory, Claude picks which; **`{type: tool, name}`** = mandatory and you pick; **`none`** = tools forbidden. `disable_parallel_tool_use: true` suppresses multiple calls in one turn.

### Q3 - Structured errors vs raising (Topic 2.2)
- **Answer given:** raising exits the loop and loses state; a structured error in `tool_result` keeps Claude reasoning; `isRetryable: true` = temporary, pause/retry; `false` = change path (escalate, skip, inform); `errorCategory` gives semantic context so recovery is not guessed from a raw string.
- **Verdict:** Pass. Correct on the loop-preservation reason and the behavioral split by retryability.
- **Explanation:** Raising an exception exits the agentic loop and discards Claude's in-progress state. Returning a **structured error** inside `tool_result` keeps the loop alive so Claude can recover. The payload steers that recovery: **`isRetryable: true`** marks a transient failure (retry the same call), `false` means change course (escalate, skip, or inform the user), and **`errorCategory`** gives a semantic label so the recovery is chosen from meaning, not parsed out of a raw string.

### Q4 - Managed vs hand-rolled loop (Topic 2.1)
- **Answer given:** hand-rolled appends messages and branches on `stop_reason`; managed runtime holds state internally; check the surfaced completion signal, not the raw stream; prose is the wrong stop condition in both because Claude emits confident text mid-reasoning before the next tool call.
- **Verdict:** Pass. Correct mechanics both sides. Sharpening: the managed completion signal is **`session.status_idle`**.
- **Explanation:** A **hand-rolled** loop owns the message list and branches on `stop_reason` itself. A **managed** runtime (Console/SDK agents) holds conversation state server-side; the caller polls a surfaced completion signal - **`session.status_idle`** - rather than the raw token stream. In both cases prose is the wrong stop condition: Claude routinely emits confident-sounding text mid-task, just before its next tool call, so only the structured signal reliably marks completion.

### Remediation
- Re-study the four `tool_choice` modes until `any` vs `auto` is automatic, and the `disable_parallel_tool_use` direction is memorized. Segment 2 tool-design half and exam-mastery Part 2 both drill this; the Day 2 gate tests it directly.
- Before re-running `/check_prog domain 2`: confirm the objective bars - managed 01/02 read (2.1); Segment 2 tools half + `domain-2` scaffold + exam-mastery Part 2 (2.2); and for 2.3 the **hard bar**: a sidecar bound its port and a live tool was invoked through the Inspector or CLI REPL.
- Topic 2.3 also needs a real quiz on **MCP transports (stdio / HTTP-SSE), config scopes, and `list_tools` discovery** - none of that was tested here.

---

## 2026-08-17 - Day 2 re-checkpoint (Topics 2.1 + 2.2 + 2.3) - 2.1 and 2.2 ticked

**Scope:** re-close after the earlier same-day Miss. Objective bars confirmed by the learner: **A = True** (managed 01/02 read), **E = yes** (`.mcp.json` studied, managed 03 read), **D = No** (no sidecar bound, no live tool invoked). Quiz covered the `tool_choice` re-close (Topic 2.2) and MCP concepts (Topic 2.3).

**Verdict:** Pass on all four questions. **Topic 2.1 ticked** (concept already Passed + read now confirmed). **Topic 2.2 ticked** (`tool_choice` clean). **Topic 2.3 partial** - two boxes ticked, the hands-on box held open by **D = No**.

### Q1 - Match `tool_choice` to the guarantee (Topic 2.2, re-quiz)
- **Answer given:** (a) `auto`; (b) `any`; (c) `any` + `disable_parallel_tool_use: true`; (d) `{type: tool, name: record_invoice}`.
- **Verdict:** Pass. The `any` / `auto` inversion from earlier is corrected and the parallel-suppression flag is now the right value.
- **Correct framing:** **`auto`** = tool optional; **`any`** = a tool call is mandatory, Claude picks which; **`{type: tool, name}`** = mandatory and you pick; `disable_parallel_tool_use: true` forces one call per turn.

### Q2 - Cost of forcing a tool (Topic 2.2)
- **Answer given:** forcing a tool means Claude cannot emit reasoning prose before or alongside the call; versus `auto` you lose the accompanying rationale; remedies are to use `auto` and parse the text, or add a reasoning field inside the tool schema.
- **Verdict:** Pass. This is the real tradeoff, and both remedies are the production patterns.
- **Explanation:** Forcing a tool (`tool_choice` = `any` or a named tool) guarantees a call but suppresses the free-text reasoning Claude would otherwise emit alongside it - you trade the model's visible rationale for the guarantee. Two production remedies recover it: use **`auto`** and parse the accompanying text, or keep the forced call and add a **reasoning field inside the tool's input schema** so the rationale arrives as a structured argument.

### Q3 - MCP transports (Topic 2.3)
- **Answer given:** stdio for a local child process on the same machine (file accessor, CLI), no network, same-process trust; HTTP/SSE for a remote shared server across a network boundary needing explicit auth and transport security.
- **Verdict:** Pass. Process locality and the trust/deployment boundary are the right axis.
- **Explanation:** MCP transport is chosen by **where the server runs**. **stdio** launches the server as a local child process over stdin/stdout - no network, same-machine trust, right for a local file accessor or CLI. **HTTP/SSE** reaches a server across a network boundary, so it needs explicit **auth** and transport security. The deciding axis is process locality and the trust/deployment boundary, not what the tool does.

### Q4 - `list_tools` discovery (Topic 2.3)
- **Answer given:** a `list_tools` client picks up a newly added server tool automatically with zero code change; a hardcoded client must be manually updated and redeployed; the gain is decoupling the client's tool knowledge from the server's release cycle.
- **Verdict:** Pass. Correct on the decoupling win.
- **Explanation:** `list_tools` lets a client **discover a server's tools at runtime**, so a newly added server tool appears with zero client code change. A client with hardcoded tool definitions must be edited and redeployed to see it. The win is **decoupling** - the client's knowledge of available tools tracks the server's release cycle automatically instead of being pinned at build time.

### Remediation (Topic 2.3 only)
- The single open Day 2 item is the **MCP hands-on**: bring up a sidecar (Inspector or the CLI REPL), **confirm the port actually bound** (probe it, do not trust the launcher exit code), and **invoke at least one live tool** and see a real result. Then re-run `/check_prog 2.3` to tick that box and complete Topic 2.3.

**Forward note:** the **D2 achievement-tracker row** is not close to ticking - it still needs the MCP hands-on (box above) **and** a 100% pass on the 8 D2 practice questions, neither of which is done. Topics 2.1 and 2.2 are fully closed.

---

## 2026-08-17 - Topic 2.3 hands-on box ticked by exception (no quiz)

**Scope:** the learner asked to tick the open Topic 2.3 hands-on box **as an explicit exception**, without doing the MCP hands-on. No quiz was run this checkpoint - the two Topic 2.3 concepts (transports, `list_tools`) already Passed on 2026-08-17 earlier.

**Verdict:** ticked by exception. **The Definition of Done bar was not met.** Per the DoD `MCP hands-on / sidecars` row, the bar is: a sidecar actually **bound its port** (probed, not trusted from a launcher exit code) **and** at least one live tool invoked through the Inspector or CLI REPL with a real result seen. That did not happen (**D = No**, unchanged from the same-day re-checkpoint). The box was flipped at the learner's direct request only.

**What this exception does and does not mean:**
- **Does:** mark Topic 2.3 visually complete so the schedule can advance to D3.
- **Does not:** demonstrate the MCP round-trip in practice. The concept understanding stands on its own (Passed), but the hands-on experience of binding a server and calling a live tool was skipped.

**Honesty note for future-you:** this is the first tick in this ledger that bypasses the DoD. It is recorded as an exception, not a Pass, so it is not mistaken later for earned hands-on coverage. If the MCP hands-on is done later, no re-tick is needed - just update this row's Notes to reflect that the bar was met after all.

**Cascade check:** with 2.3 shown complete, **all Day 2 topics are ticked**. But the **D2 achievement-tracker row does not tick** - it independently requires a **100% pass on the 8 D2 practice questions**, which has not happened. No parent row cascades from this exception.

---

## 2026-08-18 - Day 3 checkpoint (Topics 3.1 + 3.2 + 3.3) - nothing ticked

**Scope:** all three Day 3 topics quizzed in one sitting. Objective bars confirmed by the learner: **A = yes** (Claude Code half of Segment 2 worked, output read), **B = yes** (`domain-3-claude-code.md` read), **C = yes** (exam-mastery Part 3 walked green), **D = yes** (`.claude/settings.json` + `.vscode/mcp.json` inspected, CI/CD scenario read). Four exam-shaped questions across config precedence, deterministic enforcement, plan vs headless mode, and skills.

**Verdict:** **Nothing ticked.** Q2 was a **Miss** on **hook-based deterministic enforcement** - a central Domain 3 concept and exactly what the Day 3 gate tests - which blocks Topics 3.1 and 3.2. Q3 was a **Partial** (plan mode's defining property not named), so Topic 3.3 does not tick either. **A green cell is not done - being able to explain it is.**

### Q1 - Configuration precedence: managed policy vs project vs user (Topic 3.1)
- **Answer given:** enterprise/managed policy is highest in the hierarchy, so network installs are blocked entirely.
- **Verdict:** Pass. Named the governing layer and the correct outcome - the pip-vs-uv disagreement never gets to matter.
- **Explanation:** Claude Code layers configuration and the layers are **not equal**. **Enterprise / managed policy** sits at the top and cannot be overridden by any project or user file - it is administered centrally precisely so a single repo or developer cannot relax it. Beneath it, **project** configuration (the repo `CLAUDE.md` and `.claude/settings.json`, where `settings.local.json` overrides the shared `settings.json`) overrides **user** configuration (`~/.claude/CLAUDE.md`, `~/.claude/settings.json`). Highest to lowest: **managed policy > project > user**. Here the managed policy forbids network installs, so the install is blocked outright and the project/user pip-vs-uv conflict is moot. **Left out:** the project-over-user rung the question also asked for - worth stating explicitly next time.

### Q2 - Turning a suggestion into a guarantee (Topic 3.1) - the blocking miss
- **Answer given:** create a specific rule in `.claude/rules` instead of keeping the rule in `CLAUDE.md`.
- **Verdict:** Miss. A rules file is still a model-read instruction, not a runtime gate. No deterministic guarantee.
- **Explanation:** The mechanism that turns a suggestion into a guarantee is a **PreToolUse hook**. A hook is runtime code the Claude Code harness executes at a fixed **lifecycle point**; **PreToolUse** fires *before* the tool runs (here the Bash call executing `terraform apply`), inspects the pending call, and can **block or rewrite it deterministically**. A line in `CLAUDE.md` - or in `.claude/rules`, or any instruction file - is a **probabilistic guardrail**: text the model reads and usually honors, but nothing outside the model enforces it, so occasionally the model proceeds anyway (exactly the once-a-month failure described). A hook is enforced by the **system, not the model**, so it holds every time. The `.claude/rules` answer fails because it is the same class of model-read instruction as `CLAUDE.md`, with the same "usually" reliability. This is the same **PreToolUse determinism** concept from Domain 1 (Topic 1.3), applied to a Domain 3 configuration scenario.

### Q3 - Plan mode vs headless `claude -p` (Topic 3.3)
- **Answer given:** (a) headless mode; (b) plan mode. Headless is non-interactive, suitable for CI/CD pipelines; plan mode suits when a large number of changes will be made.
- **Verdict:** Partial. Both modes matched correctly and the headless rationale is right, but plan mode's *defining* property was not named.
- **Explanation:** The defining axis is **when execution happens relative to human review**. **Headless mode** (`claude -p "..."`) is non-interactive and one-shot - it runs to completion with no human in the loop and emits a result, which is exactly what a CI/CD step needs (job a). **Plan mode** is interactive and its defining property is that it **explores and proposes an approach without editing any files until you approve** - review-before-mutation. For the risky 30-file refactor (job b) that pre-execution review is the safety you want. "Suitable when a large number of changes will be made" describes a symptom, not the property: what makes plan mode right for (b) is that **no file is touched until the plan is approved** - the file count is incidental.

### Q4 - Which is NOT true about skills / slash commands (Topic 3.2)
- **Answer given:** (iii) is false.
- **Verdict:** Partial. Correct pick, but the *why* - for (iii) and for the true options - was not stated, which the exam rewards on "which is NOT true" items.
- **Explanation:** Statement **(iii) is false**. Skills load their instructions **into the current turn** so Claude follows them in place of its default approach **in the main conversation**; some skills are additionally designed to run in a subagent, but "only in a separate subagent, never in the main context" is wrong - in-context is the default. The others are true: **(i)** a skill packages a multi-step workflow with its own instructions; **(ii)** a slash command such as `/check_prog` is exactly how a user invokes a skill by name; **(iv)** a skill's instructions can override Claude's default approach for that turn, which is the whole point of invoking one.

### Remediation
- **Q2 is the one blocking miss.** Re-study **hook-based deterministic enforcement**: a hard guarantee lives in a **PreToolUse hook**, never in an instruction file (`CLAUDE.md`, `.claude/rules`). Re-read [`hooks-example.py`](hooks-example.py) and the hooks section of [`docs/domain-3-claude-code.md`](docs/domain-3-claude-code.md). This is the same concept that Passed on 2026-08-14 for Domain 1 - carry it forward into the Domain 3 config framing.
- Sharpen **plan mode's defining property**: no edits until the approach is approved (review-before-mutation), not "many changes."
- On **"which is NOT true"** items, always articulate why each distractor holds or fails - naming the false option alone is a Partial.
- Fill the **project-over-user** rung of the config hierarchy so the full order (managed policy > project > user) is automatic.
- Re-walk exam-mastery **Part 3** (TS3.1-TS3.6) on settings/hooks/precedence, then re-run **`/check_prog topic 3.1 3.2 3.3`**.

**Forward note:** the **D3 achievement-tracker row** is far from ticking - beyond re-closing these three topics it independently needs a **100% pass on the 20 D3 practice questions** (the largest bank in the repo). Nothing cascaded this run.

---

## 2026-08-18 - Day 3 re-checkpoint (Topics 3.1 + 3.2 + 3.3) - all three ticked

**Scope:** cold re-quiz of the three Day 3 topics after the same-day Miss/Miss/Partial. Objective bars stand from the earlier run (Segment 2 Claude Code half worked and output read; `domain-3-claude-code.md` read; exam-mastery Part 3 walked green; `.claude/settings.json` + `.vscode/mcp.json` inspected; CI/CD scenario read). Four exam-shaped questions, re-framed scenarios, targeting exactly the concepts that had failed: full config precedence, deterministic enforcement, plan vs headless, and the skills "which-is-NOT-true" reasoning.

**Verdict:** **Pass on all four.** Topics 3.1, 3.2, and 3.3 ticked in the plan. Every earlier gap closed.

### Q1 - Configuration precedence with a managed policy in play (Topic 3.1)
- **Answer given:** command blocked; order top to bottom is **managed/enterprise policy > project `.claude/settings.json` > user `~/.claude/settings.json`**. Enterprise policy is organization-wide baseline security; project overrides user because it is repo-specific team intent; the `npm install` fails at the enterprise layer regardless of the lower layers.
- **Verdict:** Pass. Full order stated, including the **project-over-user** rung that was left out on the first attempt, with the right rationale for each layer.
- **Explanation:** Claude Code layers configuration and the layers are **not equal**. **Managed / enterprise policy** sits at the top and cannot be overridden by any project or user file - it is administered centrally so a single repo or developer cannot relax it. Beneath it, **project** configuration (repo `CLAUDE.md`, `.claude/settings.json`, with `settings.local.json` overriding the shared `settings.json`) overrides **user** configuration (`~/.claude/CLAUDE.md`, `~/.claude/settings.json`). Highest to lowest: **managed policy > project > user**. Here the managed policy forbids public-internet access, so the install is blocked outright and the project-vs-user disagreement never matters.

### Q2 - Turning a guideline into a guarantee (Topic 3.1) - the concept that was the blocking Miss
- **Answer given:** use a runtime approval gate / **command approval hook** that acts **just before tool execution** - after Claude decides to run the command but before the process starts. It is a hard guarantee because it is enforced by application runtime code, not the model's training; `CLAUDE.md` is text the model reads and probabilistically follows, whereas a code-level gate blocks execution and Claude cannot circumvent it.
- **Verdict:** Pass. This is the deterministic-enforcement concept, correctly located at the **pre-execution** lifecycle point and correctly contrasted against the probabilistic instruction file. Directly reverses the earlier Miss (which proposed another instruction file, `.claude/rules`).
- **Explanation:** The mechanism is a **PreToolUse hook** - runtime code the Claude Code harness executes at a fixed lifecycle point. **PreToolUse** fires *before* the tool runs (here the Bash call executing `kubectl delete`), inspects the pending call, and can **block or rewrite it deterministically**. A line in `CLAUDE.md` (or any instruction file) is a **probabilistic guardrail**: text the model usually honors but nothing outside the model enforces, so it occasionally proceeds anyway - the once-a-sprint failure. A hook is enforced by the **system, not the model**, so it holds every time. Sharpening for exam vocabulary: name it the **PreToolUse hook** specifically - "approval gate" and "command hook" describe it correctly, but the exam uses the lifecycle-event name.

### Q3 - Plan mode vs headless `claude -p` (Topic 3.3)
- **Answer given:** (a) headless mode - the CI step runs unattended, human review not feasible, needs fast direct execution; (b) plan mode - the developer sees the proposed edits and commands before they execute and can approve or iterate. Defining difference for (b): plan mode **displays intent before execution, allowing human review and approval**; headless executes immediately.
- **Verdict:** Pass. Both modes matched, and plan mode's defining property - **review before any file is written to disk** - is now named, which was the Partial on the first attempt.
- **Explanation:** The defining axis is **when execution happens relative to human review**. **Headless mode** (`claude -p "..."`) is non-interactive and one-shot: it runs to completion with no human in the loop and emits a result - exactly what a CI/CD step needs (job a). **Plan mode** is interactive and its defining property is that it **explores and proposes an approach without editing any files until you approve** - review-before-mutation. For the risky multi-file refactor (job b) that pre-execution review is the safety you want; the file count is incidental, the no-mutation-until-approval property is what makes it fit.

### Q4 - Which is NOT true about skills / slash commands (Topic 3.2)
- **Answer given:** **(iii) is false** - skills are not isolated to subagents; they load their instruction packages into the current turn and can run in the main conversation's context. (i) true - a skill packages instructions for a task and invoking it loads them into the turn; (ii) true - a user invokes a skill by name with a slash command; (iv) true - a skill's instructions can override Claude's default approach for that turn.
- **Verdict:** Pass. Correct false pick **and** the *why* for the false option and each true option stated - the articulation that was missing on the first attempt.
- **Explanation:** Statement **(iii) is false**. Skills load their instructions **into the current turn** so Claude follows them in place of its default approach **in the main conversation**; some skills are additionally designed to run in a subagent, but "only in a separate subagent, never in the main context" is wrong - in-context is the default. The others hold: **(i)** a skill packages a multi-step workflow with its own instructions; **(ii)** a slash command such as `/check_prog` is exactly how a user invokes a skill by name; **(iv)** overriding Claude's default approach for that turn is the whole point of invoking one. On "which is NOT true" items the exam rewards stating why each option holds or fails - done cleanly here.

**Forward note:** all three Day 3 topics are now closed and the **Day 3 gate** (which `CLAUDE.md` wins on conflict; which hook fires relative to a tool call and what you would do there) is defended cold. The **D3 achievement-tracker row still does not tick** - it independently needs a **100% pass on the 20 D3 practice questions**, the largest bank in the repo, which has not been done. Per the replan, next is **Day 2 gate confirmation + the D3 gate** already met, then **Replan Day 2: Topic 4.1 (structured output)** - Segment 3 invoice extractor.

---

## 2026-08-19 - Day 4 checkpoint (Topics 4.1 + 4.2 + 4.3) - all three ticked

**Scope:** all three Day 4 topics quizzed in one sitting. Objective bars confirmed by the learner: Segment 3 invoice extractor ran green and output read, `domain-4-prompts.md` read, exam-mastery **Part 4** walked, managed **04** read (4.1); `domain-5-context.md` read, exam-mastery **Part 5** walked, managed **05** read (4.2); `segment-2-5-control-surfaces.ipynb` ran green and output read (4.3). Eight exam-shaped, scenario-framed questions across D4 structured output, D5 context/reliability, and the all-domain control surfaces.

**Verdict:** **Pass on all eight.** Topics 4.1, 4.2, and 4.3 ticked in the plan. Clean first-pass, no re-quiz needed. This completes **all of Day 4** and its gate.

### Q1 - Why the validation-retry loop survives forced tool use (Topic 4.1)
- **Answer given:** forcing a named tool guarantees a tool call shaped like the schema, but not that the values are semantically correct or valid for application logic - wrong values, impossible dates, hallucinated content, type-coercion edges, violated business constraints, source inconsistencies. Structure vs truthfulness/business validity.
- **Verdict:** Pass. The exact structure-vs-validity distinction the retry loop exists to cover.
- **Explanation:** Forcing `tool_choice` to a named tool guarantees the response arrives as a `tool_use` block whose arguments match the JSON **schema shape** - required fields present, types declared. It does **not** guarantee the values are *valid*: a date can be well-typed but impossible, an amount can be the wrong number, a field can be confidently hallucinated. Schema shape is a syntactic guarantee; **semantic and business-rule validity** is not, so a Pydantic (or equivalent) validation layer with **retry on failure** re-prompts with the error until the values hold. Shape is free; validity is earned.

### Q2 - Prompt-only JSON vs forced tool use (Topic 4.1)
- **Answer given:** (1) the model may emit malformed/non-parseable output despite "JSON only" - extra prose, truncation, bad escaping, drift; (2) even if it parses, it may omit required fields, invent extras, or use wrong types because prompt-following is probabilistic. Forced `tool_choice` + schema constrains into the tool interface and validates argument structure first.
- **Verdict:** Pass. Both failure modes are real and correctly attributed to prompt-following being probabilistic.
- **Explanation:** "Return valid JSON only" in the prompt is a **probabilistic** instruction - the model usually complies but nothing enforces it, so you get two failure classes: **unparseable output** (prose wrappers, truncation from `max_tokens`, invalid escaping) and **parses-but-wrong-shape** (missing required keys, invented keys, wrong types). Forced `tool_choice` with a declared input schema moves the guarantee from the prompt to the **API contract**: the response is constrained to the tool interface and its argument structure is validated before your code consumes it. That is why forced tool use is the production pattern for structured extraction, not prompt-engineered JSON.

### Q3 - What `cache_control` buys, and what to watch (Topic 4.1)
- **Answer given:** pinning the vendor policy / case-facts block lets repeated requests reuse the same prompt prefix, cutting latency and token cost across many calls; the content must stay byte-for-byte stable (ordering, formatting) or the cache misses.
- **Verdict:** Pass. Correct on the win and the primary caveat.
- **Explanation:** `cache_control` marks a stable prompt prefix as cacheable, so subsequent calls that share it pay the **cache-read** rate instead of re-processing those tokens - lower latency and cost across a batch of invoices. The prefix must be **byte-for-byte identical** across calls (any reordering or reformatting breaks the match). **Sharpening added in the quiz:** the cached prefix must also clear the **model-specific cacheable-prefix floor** or caching silently no-ops at `cache_creation=0, cache_read=0` while the exit code stays clean - **Haiku 4.5's floor is 4096 tokens** (4x Sonnet's 1024), the classic trap when flipping a demo from Sonnet to Haiku.

### Q4 - Two context-preservation moves and their tradeoffs (Topic 4.2)
- **Answer given:** (1) summarization/compression - replace older turns and bulky tool output with a concise state summary; preserves continuity but sacrifices detail and may drop subtle facts. (2) retrieval/external memory - move outputs/state into a store and selectively re-inject only relevant pieces; preserves raw information long term but trades away full-context visibility and adds retrieval complexity/risk.
- **Verdict:** Pass. Two genuinely distinct strategies, each with its real cost.
- **Explanation:** As context fills, two families of moves keep an agent reliable. **Compaction / summarization** replaces old turns and verbose tool output with a distilled summary - it holds the thread within the window but is lossy, so subtle facts can vanish. **External memory / retrieval** offloads history to a store (a database or vector index) and re-injects only what a step needs - it preserves detail indefinitely but no longer has everything in view at once and adds retrieval failure modes (wrong chunk, missed chunk). The tradeoff axis is **lossy-but-simple vs lossless-but-complex**.

### Q5 - Legitimate escalation triggers, and why "unsure" alone is not one (Topic 4.2)
- **Answer given:** triggers include required authority/approvals the model cannot provide; safety/legal/compliance/fraud/high-risk-financial situations; missing information only a human can obtain; conflicting or unverifiable evidence; repeated failed attempts despite retries; explicit user request for a human; irreversible or materially impactful actions. "Unsure" alone is insufficient because uncertainty must be operationalized into a concrete condition - low confidence on a critical field, inability to verify, repeated invalid output, policy ambiguity, elevated risk - since a model can express uncertainty even when there is enough grounded information to proceed safely.
- **Verdict:** Pass. Broad, correct trigger set, and the key insight that uncertainty must be made concrete before it becomes an escalation rule.
- **Explanation:** Escalation is a **design decision**, not a reflex. Legitimate triggers are conditions the agent genuinely cannot resolve or must not resolve alone: **missing authority** (an approval only a human holds), **high-stakes or irreversible actions**, **safety/legal/compliance/fraud** exposure, **unverifiable or conflicting evidence**, **repeated failure after retries**, and an **explicit human request**. Bare "the model is unsure" is not a trigger until it is **operationalized** - tied to a measurable condition such as a confidence score below threshold on a critical field, or inability to verify a fact - because models routinely voice uncertainty while still holding enough grounded information to proceed. A well-designed system escalates on concrete signals, not on a mood.

### Q6 - What provenance requires and why it matters (Topic 4.2)
- **Answer given:** attach the origin of the value - source document identifier plus the exact evidence location (page, bounding box, text span, quote, OCR region) it was derived from. It matters because when the number is disputed, humans and downstream systems need an auditable trail to check whether the extraction was right, OCR failed, or the source was ambiguous.
- **Verdict:** Pass. Names the two pieces (source id + evidence location) and the reliability payoff (auditability).
- **Explanation:** **Provenance / source attribution** means every extracted value carries where it came from: the **source document id** and the **specific evidence location** (page, span, bounding box, quote). This is a reliability property, not a nicety - when a figure is later disputed, the trail lets a human or a downstream system trace the number back to its origin and decide whether the model erred, the OCR misread, or the source itself was ambiguous. Without provenance an extraction is unfalsifiable; with it, every value is auditable.

### Q7 - Four control levers: tool behavior vs output mechanics (Topic 4.3)
- **Answer given:** `tool_choice` - whether the model may choose tools freely, must call a named one, or none; for deterministic routing / guaranteed extraction; bounds **tool behavior**. `disable_parallel_tool_use` - whether multiple tools fire in one turn; for ordering dependencies, side effects, shared state, cost; bounds **tool behavior**. `stop_sequences` - halt generation when a string appears; to cut at application-defined boundaries; bounds **output mechanics**. `max_tokens` - cap generated tokens; for cost/latency/length/overflow; bounds **output mechanics**.
- **Verdict:** Pass. All four correct, and the tool-behavior vs output-mechanics split is exactly right.
- **Explanation:** Two of these govern **tool behavior** - **`tool_choice`** (optional `auto` / mandatory `any` / mandatory-and-named / forbidden `none`) and **`disable_parallel_tool_use`** (one call per turn vs many) - and two govern **output mechanics** independent of tools - **`stop_sequences`** (halt the instant a named string is emitted; `stop_reason: stop_sequence`) and **`max_tokens`** (hard length ceiling; `stop_reason: max_tokens`). Keeping the two families separate is the point: one shapes *what the model does with tools*, the other *how much text comes back and when it stops*.

### Q8 - Evolving MCP server: discovery vs hardcoded clients (Topic 4.3)
- **Answer given:** the `list_tools` client discovers the new tool automatically and can use it immediately if its orchestration supports dynamic tools; the hardcoded client sees nothing until its code/config is updated and redeployed. Lesson: clients against evolving servers should favor runtime capability discovery and schema-driven integration over fixed assumptions. Bonus: `max_tokens` signals generation stopped at the token limit before the model naturally completed.
- **Verdict:** Pass. Correct on both client outcomes, the decoupling lesson, and a valid rarer `stop_reason`.
- **Explanation:** `list_tools` lets a client **discover a server's tools at runtime**, so a newly added tool appears with zero client code change; a client with **hardcoded** definitions must be edited and redeployed to see it. The design lesson is **decoupling** - build clients that read capabilities at runtime so they track the server's release cycle automatically. On stop reasons: beyond `end_turn` and `tool_use`, the learner correctly named **`max_tokens`** (hit the length ceiling mid-response); the truly rare ones worth recognizing are **`pause_turn`** (a long-running turn suspended to be continued) and **`refusal`** (the model declined on safety grounds).

**Forward note:** Day 4 is complete and its gate is defended cold. Per the replan, next is **Replan Day 2 (2026-08-19): Topic 5.1 (coverage-complete exam-mastery pass + managed 06 read), then 5.2 (Segment 4 capstone + the 60-question bank), then 5.3 (remediate + Anthropic's official Practice Exam)**. Note the **achievement-tracker rows for D4 and D5 do not tick yet** - each independently requires a **100% pass on its practice questions** (D4: 9, D5: 12), which happens in Topic 5.2. No parent rows cascaded this run.
