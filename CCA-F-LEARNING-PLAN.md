# CCA-F 5-Day Learning Plan

A self-paced path through **all study material in this repo**, divided by CCA-F exam domain, built for **5 days at up to 8 hours/day** (≈40 hours). A **7-day split** is in the appendix if you want a lighter daily load.

**How to use this file:** work top to bottom. Tick each `[ ]` as you finish it - either run **`/check_prog <topic>`** (it verifies the bar, quizzes you, and ticks the box for you), or edit the file by hand. Every day ends with a **Day gate** - a short self-check that tells you whether to move on or re-walk. The final **Exam-readiness gate** is your go/no-go before booking the proctored attempt. If your timeline slips or opens up, run **`/replan <days>`** (for example `/replan 3d`) to re-fit the remaining unchecked work into the days you actually have.

**What "done" means:** a box only flips when its [**Definition of Done**](docs/CCA-F-DEFINITION-OF-DONE.md) is met. The rule in one line: *a green cell is not done - being able to explain it is.* The [`/check_prog`](.claude/commands/check_prog.md) command enforces that rule (and quizzes you) before ticking; run it with no argument any time for a read-only progress report and an optional checkpoint quiz.

> **Ground rule from the repo:** this material is **calibration, not a braindump**. Before you schedule the real exam, take **Anthropic's official Practice Exam** (linked from your CCA-F Skilljar page). See [`docs/CERT-PROGRAM-BRIEFING.md`](docs/CERT-PROGRAM-BRIEFING.md) for mechanics: **60 questions, 120 minutes, 720 to pass, one attempt**.

---

<!-- REPLAN:START -->
## Replanned schedule (target: 2026-08-20, 3 days, replanned 2026-08-17)

**27 of 49** study items done; **~21 hours** of study remaining; **~7 hrs/day** over 3 days. Under the 8 hrs/day ceiling, so nothing is trimmed - all load-bearing material stays in. **Domain 1 is fully closed** and Domain 2 is one hands-on item short; the three replan days carry the open D2 item plus all of **D3, D4, D5, and integration**.

### Replan Day 1 - 2026-08-18 (~7.5 hrs) - finish D2, then all of D3
- [`Topic 2.3 - MCP hands-on`](#topic-23---mcp-hands-on-25-hrs) - the one open D2 item: bring up the MCP sidecars (Inspector on 6274, CLI REPL), **probe the port to confirm it bound**, and invoke a live tool.
- [`Topic 3.1 - Configuration hierarchy`](#topic-31---configuration-hierarchy-25-hrs) - Claude Code half of Segment 2 + [`docs/domain-3-claude-code.md`](docs/domain-3-claude-code.md).
- [`Topic 3.2 - D3 exam mapping`](#topic-32---d3-exam-mapping-2-hrs) - exam-mastery Part 3 + this repo's `.claude/settings.json` and `.vscode/mcp.json` as live config.
- [`Topic 3.3 - Workflows: CI/CD + headless`](#topic-33---workflows-cicd--headless-2-hrs) - the CI/CD scenario walkthrough + `claude -p` headless vs plan mode.
- **Gates:** the **Day 2 gate** (three schema/description fixes for a mis-called tool; each `tool_choice` mode to its guarantee) - concept half already defended, so confirm it holds once the hands-on is done - and the **Day 3 gate** (which `CLAUDE.md` wins on conflict; which hook fires **after** a tool result and what you would do there).

### Replan Day 2 - 2026-08-19 (~6.5 hrs) - D4 + D5
- [`Topic 4.1 - Structured output`](#topic-41---structured-output-25-hrs) - Segment 3 invoice extractor (forced `tool_choice`, Pydantic **retry on validation error**, `cache_control` pinning) + [`docs/domain-4-prompts.md`](docs/domain-4-prompts.md) + exam-mastery Part 4 + **read** managed 04.
- [`Topic 4.2 - Context management & reliability`](#topic-42---context-management--reliability-2-hrs) - [`docs/domain-5-context.md`](docs/domain-5-context.md) + exam-mastery Part 5 + **read** managed 05.
- [`Topic 4.3 - Control surfaces depth pass`](#topic-43---control-surfaces-depth-pass-2-hrs-all-domains) - Segment 2.5 (`tool_choice` + `disable_parallel_tool_use`, rare `stop_reason` values, `stop_sequences`/`max_tokens` levers, MCP `list_tools`, the Console asset surface).
- **Gate:** the **Day 4 gate** (the three escalation triggers and the two provenance moves; why forced `tool_choice` guarantees schema **shape** but not **validity**, hence the retry loop).

### Replan Day 3 - 2026-08-20 (~7 hrs) - integration, capstone, readiness
- [`Topic 5.1 - Coverage-complete pass`](#topic-51---coverage-complete-pass-25-hrs) - `cca-f-exam-mastery.ipynb` **end to end** including Part 6 (30/30 coverage matrix) + **read** managed 06 capstone.
- [`Topic 5.2 - Weighted practice`](#topic-52---weighted-practice-25-hrs) - Segment 4 capstone (10-question weighted sample) + the full **60-question bank**, one domain at a time (D1:11, D2:8, D3:20, D4:9, D5:12).
- [`Topic 5.3 - Remediate + calibrate`](#topic-53---remediate--calibrate-2-hrs) - explain every wrong answer out loud, fill gaps from one authoritative cookbook, then take **Anthropic's official Practice Exam**.
- **Gate:** the **Day 5 gate = Exam-readiness gate** (see the bottom of this file) - your go/no-go before booking.

> This schedule reschedules only unchecked work. Tick items with `/check_prog`; re-run `/replan` if your timeline shifts again.
<!-- REPLAN:END -->

---

## Before Day 1: one-time setup (≈30 min, do it the night before)

- [x] Bootstrap the notebook environment (cold run ≈20s, then reuse):
      `uv run --project notebooks jupyter lab notebooks/`
- [x] Register the `claude-architect` kernel if this is a fresh clone:
      `uv run --project notebooks python -m ipykernel install --user --name claude-architect --display-name "Claude Architect (notebooks/.venv)"`
- [x] Confirm `notebooks/.env` carries `ANTHROPIC_API_KEY` (billable notebooks need it)
- [x] Read the orientation docs so you know the map before you climb it:
  - [x] [`docs/EXAM-STUDY-PATH.md`](docs/EXAM-STUDY-PATH.md) - how the repo maps to the exam
  - [x] [`docs/CERT-PROGRAM-BRIEFING.md`](docs/CERT-PROGRAM-BRIEFING.md) - exam mechanics + week-before punchlist
  - [x] [`COURSE-FLOW.md`](COURSE-FLOW.md) - the teaching arc (skim)

**Budget note:** the seven core notebooks make live API calls at roughly **$0.05 each**. `cca-f-exam-mastery.ipynb` and the `00-prerequisites/` set are the cheapest to run.

> **Blocker (confirmed 2026-08-18): `06-managed-agents/` is read-only on this org's key - `client.beta.agents` is not available.** The **Managed Agents** endpoint (`client.beta.agents`, e.g. `agents.create`) is **not provisioned for this organization**, so none of the six notebooks can execute - every attempt to create an agent fails. (The gateway surfaces it as a 403 `team_model_access_denied` against a `global-models` group that carries no callable Claude model, but the root cause is that the `client.beta.agents` route is not open to this org.) The plain Messages API accepts `claude-haiku-4-5` fine; the block is specific to the Managed Agents surface. **All six `06-managed-agents/` notebooks are therefore READ items, not run items**, and the [**Definition of Done**](docs/CCA-F-DEFINITION-OF-DONE.md) now scores them as **read-only**: read the markdown and the code in full, and be able to explain what each cell *would* demonstrate. There is **no teardown bar** - nothing is provisioned, so nothing is left to archive. To run them later, have your org admin enable the Managed Agents (`client.beta.agents`) route with a callable Claude model, or point the notebooks at a direct Anthropic key that has the endpoint.

---

## Domain weight reference (size your study time to this)

| Domain | Weight | Task statements | Reference scaffold |
|---|---:|---:|---|
| **D1** Agentic Architecture & Orchestration | **27%** | 7 (TS1.1-1.7) | [`docs/domain-1-agentic.md`](docs/domain-1-agentic.md) |
| **D2** Tool Design & MCP Integration | **18%** | 5 (TS2.1-2.5) | [`docs/domain-2-tools-mcp.md`](docs/domain-2-tools-mcp.md) |
| **D3** Claude Code Configuration & Workflows | **20%** | 6 (TS3.1-3.6) | [`docs/domain-3-claude-code.md`](docs/domain-3-claude-code.md) |
| **D4** Prompt Engineering & Structured Output | **20%** | 6 (TS4.1-4.6) | [`docs/domain-4-prompts.md`](docs/domain-4-prompts.md) |
| **D5** Context Management & Reliability | **15%** | 6 (TS5.1-5.6) | [`docs/domain-5-context.md`](docs/domain-5-context.md) |

**D1 is the heaviest.** D5 is the lightest but the **easiest to under-learn** - it feels like security common sense until the scenario questions get specific.

---

## Day 1 - Foundations + Domain 1 kickoff (Agentic, 27%)

**Goal:** understand the primitive under every agent (the Messages API loop), then meet the agentic loop as a `stop_reason` state machine.

### Topic 1.1 - The Messages API primitive (≈2.5 hrs)
The agentic loop is just `messages.create()` in a while-loop with a `stop_reason` branch. These build that one idea at a time.
- [x] `notebooks/00-prerequisites/first_request.ipynb`
- [x] `notebooks/00-prerequisites/001_requests.ipynb` (+ `_exercise`)
- [x] `notebooks/00-prerequisites/002_system_prompt.ipynb` (+ `_exercise`)
- [x] `notebooks/00-prerequisites/003_temperature.ipynb`
- [x] `notebooks/00-prerequisites/004_streaming.ipynb`
- [x] `notebooks/00-prerequisites/005_controlling_output.ipynb` (+ `_exercise`)
- [x] `notebooks/00-prerequisites/multi_turn_conversation.ipynb`

### Topic 1.2 - The agentic loop (≈2.5 hrs)
- [x] Run `notebooks/segment-0-pre-flight.ipynb` (SDK floor check, credentials)
- [x] Work `notebooks/segment-1-customer-support-agent.ipynb` end to end - watch `stop_reason` flip `end_turn` -> `tool_use`, the dispatcher, the `tool_result` contract
- [x] Read [`docs/domain-1-agentic.md`](docs/domain-1-agentic.md) (loop, coordinator-subagent, hooks, sessions, task decomposition)

### Topic 1.3 - D1 exam mapping (≈1.5 hrs)
- [x] Walk **Part 1** of `notebooks/cca-f-exam-mastery.ipynb` (TS1.1-TS1.7)
- [x] Read [`hooks-example.py`](hooks-example.py) and [`coordinator-subagent-sketch.py`](coordinator-subagent-sketch.py)

**Day 1 gate:** Can you explain, out loud, (a) what makes the loop terminate, (b) why a **PreToolUse hook** is a deterministic guarantee where a prompt instruction is not, and (c) why coordinator-subagent isolation matters? If not, re-walk exam-mastery Part 1.

---

## Day 2 - Domain 1 depth + Domain 2 (Tools & MCP, 18%)

**Goal:** contrast a hand-rolled loop with Anthropic's managed loop, then learn that a **tool description is the contract** and MCP is how tools arrive.

### Topic 2.1 - Managed agents = the loop, hosted (≈1.5 hrs, READ-ONLY - see blocker)
- [x] **Read** `notebooks/06-managed-agents/01_agentic_loop_and_sessions.ipynb` (D1) - do not run; gateway blocks `agents.create`
- [x] **Read** `notebooks/06-managed-agents/02_coordinator_and_subagents.ipynb` (D1)
- [x] For each: read the markdown and code, and be able to explain how the **managed** loop differs from the hand-rolled Segment 1 loop - the **session** holds conversation state server-side, and you stop on **`session.status_idle`** / **`stop_reason`**, not on assistant text looking finished

### Topic 2.2 - Tool design (≈2.5 hrs)
- [x] Work the **tool-design half** of `notebooks/segment-2-tool-design-and-mcp.ipynb` - opinionated descriptions, the four `tool_choice` modes, structured errors (`is_error` vs the camelCase `isError` payload)
- [x] Read [`docs/domain-2-tools-mcp.md`](docs/domain-2-tools-mcp.md)
- [x] Walk **Part 2** of `cca-f-exam-mastery.ipynb` (TS2.1-TS2.5)

### Topic 2.3 - MCP hands-on (≈2.5 hrs)
- [x] Study [`.mcp.json`](.mcp.json) - 6 servers, 3 transports, env-var expansion; note it is the **only** project-scoped MCP file Claude Code reads
- [x] Bring up the MCP sidecars and poke a live server (Inspector on 6274, CLI REPL): see the launcher notes in [`CLAUDE.md`](CLAUDE.md) and [`examples/mcp_cli/`](examples/mcp_cli/) - **ticked by exception 2026-08-17: hands-on deferred, concepts already Passed. Not a met DoD bar.**
- [x] **Read** `notebooks/06-managed-agents/03_tools_and_structured_errors.ipynb` (D2, with the D3 bridge) - read-only; gateway-blocked

**Day 2 gate:** Given a tool that the model keeps calling wrong, can you name three fixes that live in the **description/schema** rather than the prompt? Can you match each `tool_choice` mode to the guarantee it gives?

---

## Day 3 - Domain 3 (Claude Code Configuration & Workflows, 20%)

**Goal:** the configuration surface - `CLAUDE.md` hierarchy, `settings.json`, hooks, skills, plan vs headless mode, and CI/CD. This is the **most practice-question-heavy domain in the repo (20 questions)**.

### Topic 3.1 - Configuration hierarchy (≈2.5 hrs)
- [x] Work the **Claude Code half** of `notebooks/segment-2-tool-design-and-mcp.ipynb` - CLAUDE.md hierarchy audit, prompt caching, path-specific rules
- [x] Read [`docs/domain-3-claude-code.md`](docs/domain-3-claude-code.md) (CLAUDE.md hierarchy, skills/slash commands, path rules, plan vs `claude -p`, settings/hooks/MCP, iterative refinement)

### Topic 3.2 - D3 exam mapping (≈2 hrs)
- [x] Walk **Part 3** of `cca-f-exam-mastery.ipynb` (TS3.1-TS3.6)
- [x] Inspect this repo's own `.claude/settings.json` and `.vscode/mcp.json` as live examples of the config surface

### Topic 3.3 - Workflows: CI/CD + headless (≈2 hrs)
- [x] Read [`docs/scenario-cicd-integration.md`](docs/scenario-cicd-integration.md) - the CI/CD scenario walkthrough
- [x] Understand `claude -p` headless mode and plan mode as distinct workflows (domain-3 covers both)

**Day 3 gate:** Can you predict which `CLAUDE.md` wins when project and user files conflict? Can you say which hook lifecycle event fires **after** a tool result, and one thing you would do there? These are exactly the question shapes in the D3 bank.

---

## Day 4 - Domain 4 (Prompts & Structured Output, 20%) + Domain 5 (Context & Reliability, 15%)

**Goal:** guaranteed structured output via forced tool use + validation retry, then the reliability layer - context preservation, escalation, provenance.

### Topic 4.1 - Structured output (≈2.5 hrs)
- [ ] Work `notebooks/segment-3-invoice-extractor.ipynb` - forced `tool_choice`, Pydantic validation with **retry on validation error**, case-facts pinning via `cache_control`, confidence-routing triage. (This notebook and one exam-mastery cell use **Sonnet 4.6** on purpose - the reasoning-depth exception.)
- [ ] Read [`docs/domain-4-prompts.md`](docs/domain-4-prompts.md)
- [ ] Walk **Part 4** of `cca-f-exam-mastery.ipynb` (TS4.1-TS4.6)
- [ ] **Read** `notebooks/06-managed-agents/04_structured_output_and_validation.ipynb` (D4) - **read-only** (`client.beta.agents` not provisioned for this org; do not run)

### Topic 4.2 - Context management & reliability (≈2 hrs)
- [ ] Read [`docs/domain-5-context.md`](docs/domain-5-context.md) - context preservation, escalation design, error propagation, provenance/source attribution
- [ ] Walk **Part 5** of `cca-f-exam-mastery.ipynb` (TS5.1-TS5.6)
- [ ] **Read** `notebooks/06-managed-agents/05_context_and_escalation.ipynb` (D5) - **read-only** (`client.beta.agents` not provisioned for this org; do not run)

### Topic 4.3 - Control surfaces depth pass (≈2 hrs, all domains)
- [ ] Work `notebooks/segment-2-5-control-surfaces.ipynb` - full `tool_choice` + `disable_parallel_tool_use`, the rare `stop_reason` values, `stop_sequences`/`max_tokens` as levers, MCP `list_tools`, and the live Console asset surface (`memory_stores`, `vaults`, `agents`, `sessions`)

**Day 4 gate:** Can you state the **three legitimate escalation triggers** and the two moves for provenance? Can you explain why forced `tool_choice` guarantees schema shape but not schema **validity** (hence the retry loop)?

---

## Day 5 - Integration, capstone, and exam readiness

**Goal:** one coverage-complete pass, then calibrate against the weighted practice bank, then remediate weak domains.

### Topic 5.1 - Coverage-complete pass (≈2.5 hrs)
- [ ] Walk `notebooks/cca-f-exam-mastery.ipynb` **end to end**, including **Part 6** (exam mechanics, the 30-task-statement coverage matrix, one-page cheat sheet, self-check). It self-audits **30/30** task statements.
- [ ] **Read** `notebooks/06-managed-agents/06_cca_f_capstone.ipynb` (all domains) - **read-only** (`client.beta.agents` not provisioned for this org; do not run)

### Topic 5.2 - Weighted practice (≈2.5 hrs)
- [ ] Work `notebooks/segment-4-cca-f-capstone.ipynb` (cert briefing + the 10-question domain-weighted live sample)
- [ ] Take the full **60-question bank** in [`docs/PRACTICE-QUESTIONS.md`](docs/PRACTICE-QUESTIONS.md), **one domain at a time** (D1:11, D2:8, D3:20, D4:9, D5:12). Get a domain to 100%, then mix.

### Topic 5.3 - Remediate + calibrate (≈2 hrs)
- [ ] For every wrong answer, **explain it out loud** - not "the answer was C" but *why* the distractors fail. If you can't, re-read that domain scaffold.
- [ ] Fill any gap with one authoritative cookbook - check [`docs/COOKBOOK-INDEX.md`](docs/COOKBOOK-INDEX.md) first (two cookbooks fail on upstream bugs; don't lose an evening to them)
- [ ] Take **Anthropic's official Practice Exam** from your Skilljar page - your last calibration before the one-shot proctored attempt

**Day 5 gate = Exam-readiness gate** (see bottom).

---

## Achievement tracker (domain coverage at a glance)

Tick a domain once you've done its notebook work, read its scaffold, walked its exam-mastery part, **and** scored 100% on its practice questions.

- [ ] **D1** Agentic Architecture & Orchestration (27%) - Segment 1, managed 01/02 (read), exam-mastery P1, domain-1, 11 questions
- [ ] **D2** Tool Design & MCP Integration (18%) - Segment 2 (tools), managed 03 (read), MCP sidecars, exam-mastery P2, domain-2, 8 questions
- [ ] **D3** Claude Code Configuration & Workflows (20%) - Segment 2 (Claude Code), exam-mastery P3, CI/CD scenario, domain-3, 20 questions
- [ ] **D4** Prompt Engineering & Structured Output (20%) - Segment 3, managed 04 (read), exam-mastery P4, domain-4, 9 questions
- [ ] **D5** Context Management & Reliability (15%) - managed 05 (read), Segment 2.5, exam-mastery P5, domain-5, 12 questions
- [ ] **Coverage-complete** - full exam-mastery pass (30/30 task statements) + managed 06 capstone (read)

> **Note:** the six `06-managed-agents/` items are **read, not run** on this key (see the blocker at the top). A managed row counts as met when you have read the notebook and can explain it - the runnable core notebooks and exam-mastery parts carry the hands-on coverage for every domain.

---

## Exam-readiness gate (go / no-go before booking)

Book the proctored exam only when **all** of these are true:

- [ ] All five domain rows above are ticked
- [ ] Full exam-mastery notebook walked end to end, nothing in it surprises you
- [ ] 60-question bank: **90%+ overall**, and **no single domain below 80%**
- [ ] You can talk through all **six named scenario families** (Customer Support, Code Generation, Multi-Agent Research, Developer Productivity, CI/CD Integration, Structured Data Extraction) and name their primary domains
- [ ] **Anthropic's official Practice Exam** taken and passed comfortably
- [ ] Week-before punchlist in [`docs/CERT-PROGRAM-BRIEFING.md`](docs/CERT-PROGRAM-BRIEFING.md) reviewed

Remember: **one attempt, $99** (partner discount may apply). Treat it like one shot.

---

## Appendix - 7-day split (lighter daily load)

Same material, one domain per day, roughly 4-6 hours/day instead of 8. Use this if 5 days at full tilt is too much.

| Day | Focus | Core items |
|---|---|---|
| **1** | Setup + Foundations | One-time setup, orientation docs, all of `00-prerequisites/`, Segment 0 |
| **2** | **D1** Agentic (27%) | Segment 1, domain-1, exam-mastery P1, managed 01/02, hooks + coordinator sketches |
| **3** | **D2** Tools & MCP (18%) | Segment 2 (tools half), `.mcp.json`, MCP sidecars, managed 03, domain-2, exam-mastery P2 |
| **4** | **D3** Claude Code (20%) | Segment 2 (Claude Code half), domain-3, exam-mastery P3, CI/CD scenario, settings/hooks |
| **5** | **D4** Prompts & Structured Output (20%) | Segment 3, domain-4, exam-mastery P4, managed 04 |
| **6** | **D5** + Control surfaces | domain-5, exam-mastery P5, managed 05, Segment 2.5 (all-domain depth) |
| **7** | Capstone + readiness | Full exam-mastery pass, managed 06, Segment 4, 60-question bank, Anthropic Practice Exam |

Run each day's **Day gate** and the final **Exam-readiness gate** exactly as in the 5-day plan - only the pacing changes.

> The `06-managed-agents/` items in Days 2, 3, 5, 6, and 7 above are **read-only** on this key - see the blocker note near the top of this file.
