# 08 · What is deliberately absent

Rejected designs, with the reasons. This is the document I would read first in
someone else's repository, because what a system does is mostly determined by
what it was built on top of, and what it was built on top of is mostly
determined by what got refused.

---

## A vector store

**What it would have given.** Semantic retrieval over the knowledge base.
Questions phrased in customer vocabulary would match pages written in catalogue
vocabulary without an explicit keyword bridge.

**Why not.** At the current scale — a curated set of pages over a catalogue in
the hundreds — deterministic keyword overlap plus review discipline outperforms
an untuned vector store, and it has three properties the vector store does not:
it cannot fail at runtime, a wrong retrieval is explainable by looking at the
data, and a non-engineer can inspect and correct it.

**The honest cost.** Class 2 in [02 · Why scripts fail](02-why-scripts-fail.md)
— *"will this survive a teenager?"* — is exactly what embeddings handle well. It
is currently handled by the reasoning layer instead, which works and is more
expensive per query.

**Revisit when.** A specific, recorded retrieval failure that keyword overlap
cannot resolve after the keywords have been corrected. The scoring function is
isolated precisely so this swap costs nothing structural when the evidence
arrives.

---

## Self-editing memory

**What it would have given.** The agent writing its own durable knowledge from
operation, without a review step. Faster learning, no weekly human minutes.

**Why not.** This is not a more mature system; it is a system with a quieter
failure mode. A wrong item enters, gets retrieved, shapes answers, and the
answers it shaped look consistent — so it reinforces itself. By the time anyone
notices, the base holds several generations of confident error and there is no
clean rollback point.

Review costs minutes a week. The cost of its absence is unbounded and is not
detectable early. That is not a close trade at this scale.

**Revisit when.** There is an evaluation set strong enough to catch a bad item
before it reaches retrieval. Review is replaceable by measurement, not by
optimism.

---

## A confidence score on the generated answer

**What it would have given.** One number per answer, a threshold, an escalation
rule. Simple to build and simple to explain.

**Why not.** A model asked to generate an answer and then rate its own confidence
produces a fluent answer and a confident score, because both come from the same
process. The failure the score is supposed to catch is exactly the one it cannot
see.

The sufficiency check runs **before** generation, against the inputs, and asks a
different question of different material. That version can come back negative.

---

## A global confidence threshold

**What it would have given.** One tunable dial for the whole system.

**Why not.** Missing information means different things in different domains. A
stock question tolerates a qualified answer; a warranty question must not produce
one, because a qualified warranty statement is read as a commitment regardless of
the qualifier. One dial gets tuned to the strictest case — making the system
useless everywhere else — or to the average, making it wrong where it matters.

`uncertainty_rule` lives on the principle card. Twenty-five small local decisions
beat one global one that cannot be right.

---

## A model call in the language detection path

**What it would have given.** Better handling of genuinely ambiguous input.

**Why not.** Language runs on every single turn. A model call there adds latency
to every message and introduces variance into something with a right answer. The
deterministic detector is arithmetic, cannot fail, and is corrected by adding a
phrase to a list — which is how the accent-less Québec French cases were fixed in
minutes rather than by prompt tuning.

---

## Prompt-level behaviour fixes

**What it would have given.** The fastest possible fix for any observed
misbehaviour: add a sentence to the prompt.

**Why not.** Tried and rolled back three times. It holds until the next unusual
input and then fails silently, and each attempt leaves a sentence in the prompt
that nobody dares remove because nobody remembers what it was protecting against.

The rule is now enforced on the code critic itself: *never propose fixing
behaviour by adding words to a prompt; a control must be code, a flag or a test.*

This is the same sentence that shapes the setup in Part I. It was learned here,
in production, at the cost of three rollbacks.

---

## A second agent

**What it would have given.** Specialist agents per domain — warranty, selection,
availability — coordinated by a router.

**Why not.** The justification for a second agent is context isolation: a subtask
requiring far more reading than the main agent should carry, returning a short
conclusion. That condition is not met here. Fact selection already keeps the
working set small, and the domains share most of their knowledge.

What a second agent adds automatically: a way for two components to hold
contradictory beliefs about the same customer, a new place for work to be
duplicated, and a new way to deadlock. Those costs are certain; the benefit was
speculative.

---

## The pattern in all seven

Each rejection follows the same shape, and it is worth naming because it is the
transferable part:

> **A capability was available. The failure it would have prevented had not
> occurred. The failure it would have introduced was structural.**

Adopting a technique because it is good is not the same as adopting it because it
solves a problem you have. Every entry above is recorded with a revisit
condition, so the question is settled rather than merely postponed — and so the
next roundup article recommending one of them does not restart the discussion
from zero.

---

**Back to:** [Part II index](../../README.md#part-ii--the-ai-administrator) ·
[Decision log](../../decisions/README.md)
