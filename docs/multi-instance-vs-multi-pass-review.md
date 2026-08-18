# Multi-Instance and Multi-Pass Review

> Study companion to **[`domain-4-prompts.md`](./domain-4-prompts.md)** - expands the "Multi-instance review architectures" section (Domain 4, task statement **TS4.6**) into a reread-friendly reference.

## Why this file exists

`domain-4-prompts.md` introduces two quality-raising techniques for high-stakes extraction and uses two names for them: **multi-instance review** (the "Multi-instance review architectures" section) and **multi-pass review** (the label on the `evaluator_optimizer.ipynb` reference). Those names point at **two different axes of spending more compute to trust the output more**. This file pulls them apart, shows where each fits Domain 4, and connects them to the cookbook references the domain doc already cites.

The one-line version:

- **Multi-pass** = the **same reviewer**, run **again in sequence**, each round changing its frame. It buys **depth**.
- **Multi-instance** = **many independent reviewers**, run **in parallel**, then combined. It buys **breadth**.

## The core distinction

| | Multi-pass | Multi-instance |
|---|---|---|
| **Shape** | 1 reviewer, N sequential rounds | N reviewers, 1 round each |
| **Axis** | Depth / iteration | Breadth / independence |
| **Failure it fixes** | Shallow first look, missed follow-through | Single blind spot, correlated bias, over-confidence |
| **Cost pattern** | Latency adds up (serial) | Tokens add up (parallel), latency stays flat |
| **Core risk** | Anchoring - later rounds defer to the first | Correlation - all instances share one blind spot |
| **Domain 4 home** | Validate-then-retry loop; evaluator-optimizer | The 2-3 independent passes plus a judge |

## Multi-pass review, in detail

A single look at an extraction tends to be **shallow**: the model produces a plausible object, states a verdict, and stops. Multi-pass forces continued scrutiny by re-entering the same material with a **changed frame** each round.

Where Domain 4 already uses it:

- **The validate-then-retry loop** (`domain-4-prompts.md`, "Validation, retry, and feedback loops") is multi-pass. Pass 1 extracts. `Model.model_validate()` raises on a schema violation. Pass 2 re-runs with the validation error appended as a user turn. This is the **same reviewer, run again**, with the frame changed by the error feedback.
- **The evaluator-optimizer pattern** (`../claude-cookbooks-main/patterns/agents/evaluator_optimizer.ipynb`) is the canonical multi-pass shape: one pass produces, a second pass critiques, a third revises against the critique, looping until the critique comes back clean.

Common multi-pass structures:

1. **Find then verify.** Pass 1 produces candidates. Pass 2 tries to **confirm each with a concrete failure case**. Unconfirmable candidates get dropped.
2. **Layered lenses.** The material is constant; the question rotates - correctness, then completeness, then "does the null-if-not-stated rule actually hold here."
3. **Loop-until-clean.** Keep passing until a round produces no new findings. Better than a fixed "review twice" when you do not know the defect count in advance.

**The load-bearing risk is anchoring.** Later passes tend to rubber-stamp the first pass's framing. Two mitigations: give each pass a genuinely different instruction, and for a verification pass prompt the reviewer to **refute** rather than confirm. This is also why the domain doc caps retries at **2-3** - past that, a multi-pass loop on a **missing-information** failure just burns tokens, because re-reading input that never contained the value cannot recover it.

## Multi-instance review, in detail

One reviewer, however careful, has **one blind spot** and **one bias**. Running it again the same way reproduces the same error. Multi-instance breaks that by asking **several independent reviewers the same question** and combining verdicts.

This is exactly the architecture in `domain-4-prompts.md`, "Multi-instance review architectures":

> Run the same prompt through **2-3 independent model passes**, then add a fourth **judge** pass that synthesizes the outputs and flags disagreements. **Agreement across passes** routes to automation; **disagreement** routes to human review. Cost is roughly **3x baseline**, and it catches systematic biases a single pass cannot.

Read that carefully: the "2-3 independent passes" are the **multi-instance** part (breadth - independent reviewers), and the **judge** on top is a **multi-pass** step (depth - a reviewer that consumes the earlier outputs). The production pattern is already a **combination** of both axes. That is the point worth remembering for **TS4.6**.

Common multi-instance structures:

1. **Agreement voting.** N independent extractions of the same document. Fields where all instances agree are high confidence; fields where they diverge are routed to a human. This is the domain doc's default.
2. **Adversarial verify.** N reviewers each prompted to **kill** a finding; keep it only if a majority fail to refute it.
3. **Perspective-diverse panel.** Rather than N identical reviewers, give each a distinct lens (a legal document reviewed for dates, for parties, for obligations). Diversity catches failure modes redundancy cannot.

**The load-bearing risk is correlation.** If every instance is the same model, same prompt, same context, their "independent" votes fail together. Mitigations: vary the prompt or the lens across instances, and require majority agreement so one confident-but-wrong instance cannot carry the result.

## Why the production pattern uses both

The Domain 4 high-stakes architecture is strong precisely because it stacks the two axes, each defending a different failure:

```text
Multi-instance (breadth):   pass A ─┐
                            pass B ─┼─►  judge  ─►  agree? automate : human review
                            pass C ─┘   (multi-pass: reads all three, synthesizes)
```

- The **independent passes** guard against a single blind spot - if one pass misreads a field, the others outvote it.
- The **judge pass** guards against shallow reconciliation - a dedicated pass that reads all outputs, flags disagreements, and decides routing rather than naively averaging.

Two rules keep the combination honest:

- **Route on the disagreement signal, not on any single pass's self-reported confidence.** The domain doc's caveat applies: confidence scores are routing keys, and you validate calibration **by document type and by field** before trusting them.
- **Scale to the stakes.** A nightly low-risk backfill does not need 3x cost - a single pass with validate-then-retry is enough. Reserve the full multi-instance-plus-judge architecture for **medical, legal, financial** extractions where a wrong field is expensive.

## Decision guide

| Situation | Use |
|---|---|
| Output failed schema validation | **Multi-pass** - retry with the error appended, cap at 2-3 |
| Output is null because the data is genuinely absent | **Neither** - retries and extra instances cannot invent missing data; escalate |
| Format is inconsistent across rows | Not a review problem - add **few-shot examples** first |
| High-stakes field must be trustworthy | **Multi-instance** (2-3 passes) **plus a judge pass** |
| You need depth on one reviewer's output | **Multi-pass** - evaluator-optimizer loop |
| You need to cancel out one reviewer's bias | **Multi-instance** - independent voters |

## Cost and when it is not worth it

- **Multi-pass retry** is cheap: it only fires on a validation failure, so most documents cost one pass.
- **Multi-instance plus judge** is roughly **3x to 4x baseline** (three extractions plus a judge). It is justified only when the cost of a wrong field exceeds that multiple. For asynchronous high-volume work, remember the domain doc's other lever - the **Message Batches API** is about 50% off, so a 3x multi-instance architecture run in batch is closer to 1.5x real-time single-pass.

## Code references

- `../claude-cookbooks-main/patterns/agents/evaluator_optimizer.ipynb` - the multi-pass produce-critique-revise loop (self-study).
- `../claude-cookbooks-main/tool_use/parallel_tools.ipynb` - parallel extraction, the mechanical basis for running instances concurrently. Read it for the pattern; it currently **fails to run** on an upstream bug (see [`COOKBOOK-INDEX.md`](./COOKBOOK-INDEX.md)).
- [`segment-3-invoice-extractor.ipynb`](../notebooks/segment-3-invoice-extractor.ipynb) - the live Domain 4 build (validate-then-retry is demonstrated here).
- [`cca-f-exam-mastery.ipynb`](../notebooks/cca-f-exam-mastery.ipynb) **Part 4** - covers **TS4.6** (multi-pass review) alongside the other five Domain 4 task statements.

## The one thing to retain for the exam

**Multi-pass = same reviewer, deeper (sequential, fixes shallowness, risks anchoring). Multi-instance = many reviewers, wider (parallel, fixes blind spots, risks correlation). The Domain 4 high-stakes pattern is both at once: 2-3 independent passes for breadth, one judge pass for depth, routed on agreement.**
