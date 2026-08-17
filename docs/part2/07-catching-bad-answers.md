# 07 · Catching a bad answer

A wrong answer in this system does not raise an exception. It is well-formed,
fluent, in the right language, and indistinguishable from a correct one until
someone acts on it.

Everything here exists because that is true.

---

## The adversarial critic

Changes to the reasoning layer are reviewed by a critic whose instructions are
written to make praise difficult. This is the actual prompt, with the project
identifiers removed:

```text
You are an adversarial code critic on a production customer-service agent.
Your job is to try to BREAK the change, not to praise it.

Rules you must follow:
- Report only defects you can point at with a file and a line from the diff.
- For each, give the concrete failure: inputs or state -> wrong behaviour.
- If you are not sure, say PLAUSIBLE, not CONFIRMED. Guessing costs more
  than silence.
- You have NOT read the project's decision registry. If something looks wrong
  but could be a deliberate owner decision, say so explicitly instead of
  asserting.
- Never propose fixing behaviour by adding words to a prompt. On this project
  that has been rolled back three times; a control must be code, a flag or
  a test.
- Say plainly if the change is sound. A critic that always finds something
  is noise.

Answer as JSON: {"findings":[{"severity":"high|medium|low",
"verdict":"CONFIRMED|PLAUSIBLE","file":"","line":0,"claim":"","failure":"",
"fix":""}],"verdict_overall":""}
```

Five of those lines were each written after a specific failure.

**"Point at a file and a line."** Without it, a critic produces architectural
opinions that cannot be acted on or verified. A finding that cannot be located is
not a finding.

**"PLAUSIBLE, not CONFIRMED."** A reviewer that states everything with equal
confidence forces you to verify everything, which costs more than the review
saved. Grading the confidence makes the output triageable.

**"You have NOT read the decision registry."** The critic is deliberately blind.
A reviewer holding the project's history rationalizes — it sees an odd choice and
assumes it was deliberate. A blind one asks why. It is wrong more often, and the
wrong ones are cheap; the right ones are the reason the review exists. This is
the check that caught a test exercising a re-implementation rather than the real
code (see [05](05-memory-and-updates.md)).

**"Never propose fixing behaviour by adding words to a prompt."** Rolled back
three times. Adding a sentence to a prompt feels like the fastest fix and holds
until the next unusual input, then fails silently. The rule is now enforced on the
reviewer so it cannot even suggest the tempting thing.

**"A critic that always finds something is noise."** Without explicit permission
to approve, a critic invents findings to justify the call, and the team learns to
ignore it. The permission is what keeps the signal alive.

---

## What is recorded per exchange

| Recorded | Used for |
|---|---|
| Message, resolved language, session language | Language failures — the most common and most visible |
| Detected intent and tags | Systematic misclassification |
| Principle cards activated | Which rule governed this, so a bad pattern is traceable to a card |
| Facts selected | Whether the right knowledge was reachable |
| Sufficiency outcome and stop reason | Where the system is blind |
| Answer sent, or the handoff | Reconstruction |

The point of recording *cards and facts* rather than only the answer: when an
answer is wrong, the question is which layer failed. Wrong facts means retrieval
or the knowledge base. Right facts and a wrong answer means the card or the
generation. Without the intermediate state, every investigation restarts from
nothing and usually ends in a prompt edit — the fix the critic is forbidden to
suggest, for good reason.

---

## What raises an alarm

Not the error rate. Errors here are mostly silent, so a low error rate is
uninformative and comfortable.

**Stop reasons clustering.** The same missing input blocking many conversations
is a specific gap: either a fact the base should hold or a rule stricter than it
needs to be. Both are fixable and neither is visible in an aggregate.

**Language resolution flipping mid-conversation.** Detectable exactly, and it
means the detector met a phrasing it does not know. This is how the accent-less
Québec French cases were found.

**Sufficiency passing on a class where it used to stop.** A gate that quietly
stopped stopping is worse than a gate that fails loudly, because the system looks
like it improved.

**Retrieval returning nothing.** A query that matched no approved page is either
vocabulary the knowledge base does not carry or a page that should exist. Both
are worth reading individually.

---

## How a failure is dissected

Fixed order, because skipping to the end is how prompt edits happen.

1. **Reproduce with the same input.** If it does not reproduce, the variance
   itself is the finding.
2. **Read the recorded intermediate state.** Which cards fired, which facts were
   selected, what the sufficiency check concluded.
3. **Locate the layer.** Language, intent, cards, facts, sufficiency, generation.
   The recording makes this a lookup rather than an argument.
4. **Fix at that layer.** A card is data and is edited and re-approved. A
   retrieval failure is keywords or a missing page. A generation failure is a
   card boundary that was too loose.
5. **Add the case to the test set.** Not to the prompt.

Step 5 is the one that compounds. Every dissected failure becomes a case that
future changes have to pass, and the set grows into a description of everything
the system is known to get right.

---

## The rule underneath all of it

> A control must be code, a flag or a test.

Three rollbacks taught it here, and it is the same sentence that shapes the
setup in Part I. A constraint that depends on the model's cooperation is not a
constraint — it is a preference that holds until the input gets unusual.

---

**Next:** [08 · What is deliberately absent](08-deliberately-absent.md)
