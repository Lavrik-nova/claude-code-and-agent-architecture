# 06 · Limits and handoff

Most of the engineering in this system is not about answering. It is about
knowing when not to.

An agent that always produces an answer is straightforward to build and expensive
to operate, because the cost of its wrong answers is paid by someone else — the
customer who acts on a false specification, the staff member who retracts a
warranty commitment that should never have been made.

---

## Three ways to stop, and they are not interchangeable

| Outcome | When | What the customer sees |
|---|---|---|
| **Targeted question** | One missing input would settle it | A single question, not a form |
| **Qualified answer** | Enough to be useful, not enough to be complete | The answer plus the stated limit |
| **Handoff** | The decision is not the system's to make | A clear handover with the context preserved |

Which one applies is decided by the governing principle card's `uncertainty_rule`
— per card, not globally. See [03 · The reasoning stack](03-reasoning-stack.md).

**Why per card.** A missing input on a stock question and a missing input on a
warranty question are different situations. One tolerates a qualified answer;
the other must not produce one, because a qualified warranty answer is read as a
commitment regardless of the qualifier. A single global confidence threshold
cannot express this. Systems built on one end up tuned to the strictest case and
therefore useless on the rest, or tuned to the average and wrong on the case that
matters.

---

## The targeted question, and why it is one question

The correct response to an underdetermined message is often a clarifying
question. The failure mode is asking all of them.

A message with four unknowns and a form-shaped reply loses the customer at
question two. The design constraint is that the system asks for **the one input
that most changes the answer**, and works with what it has for the rest —
stating the assumption it made so the customer can correct it in passing.

This is the difference between a system that clarifies and one that
interrogates, and it is why the `known_facts` field on a principle card matters:
it says which inputs the rule genuinely depends on, so the system can rank what
is missing instead of listing it.

---

## Ambiguity is a state, never a silent exclusion

A rule taken from the operational reporting side of the same system, because it
generalizes:

```python
"""status(task) -> "replied" | "open" | "ambiguous". A task is REPLIED only
when its OWN conversation thread has a Sent message strictly AFTER the task's
inbound time. This is per-thread and per-timestamp — NEVER a global Sent-time
cutoff — so a later reply to client B can never hide an earlier unanswered
client A. No linkage (missing conversation_id or unreachable mailbox) =
"ambiguous" → never silently excluded."""
```

Two rules worth extracting.

**Ambiguous is a visible third state.** When the system cannot establish whether
something was handled, it says *ambiguous* and routes it for triage. It does not
guess, and it does not drop the item. Anything that cannot be classified must
end up in front of a person — silence is the one outcome that is never
acceptable, because silence looks identical to success.

**Never a global cutoff.** The tempting implementation compares against a single
timestamp for everyone. It is simpler and it hides exactly the case that matters:
a later reply to one customer masking an earlier unanswered one. Per-thread
checking is more work and it is the only version that cannot conceal a failure.

Both rules are the same instinct as the SQL filter in
[04](04-product-knowledge.md): make the dangerous case structurally impossible
rather than relying on it being noticed.

---

## Why admitting ignorance is worth paying for

There is a real cost. "I need to check on that" is less satisfying than an
answer, and some fraction of those conversations would have been resolved by a
correct guess.

It is still the right trade, for a reason specific to this domain.

**A wrong answer here is not a bad answer — it is a commitment.** A stated
warranty eligibility, a quoted dimension, a confirmed availability: the customer
acts on these. Retracting one costs more goodwill than never having offered it,
and it costs the staff member the call that the widget existed to prevent, now
with a frustrated customer on the other end.

**The failure is invisible from the inside.** A fluent wrong answer generates no
error, no alert, no complaint in most cases — the customer simply does not come
back. A system optimized on completion rate will drift toward confident error and
its metrics will improve the whole way.

So the constraint is inverted: **the system is optimized for not being wrong,
and completion rate is the thing that is allowed to suffer.** The measurement
that matters is not how many conversations ended with an answer, but how many
ended correctly — including the ones that correctly ended with a handover.

---

## What a good handoff carries

A handoff that loses the context is a second conversation, and the customer
repeats themselves. What is preserved:

- The full exchange, in the language it happened in
- What the system determined — intent, the facts it selected, the constraint that
  blocked it
- Why it stopped: which rule, which missing input

The last item is the one usually omitted and the one that makes the handoff
useful. A staff member receiving *"could not determine warranty eligibility:
purchase date unknown, failure mode consistent with wear"* starts from a
position. One receiving *"escalated"* starts from zero.

It is also the raw material for improvement. A recurring stop reason is a
specific, actionable gap — a fact the base should hold, or a rule that is stricter
than it needs to be. Stop reasons are how the system tells you where it is
blind.

---

**Next:** [07 · Catching a bad answer](07-catching-bad-answers.md)
