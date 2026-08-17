# 06 · How I know it works

The uncomfortable document. Most write-ups of a setup like this end with a
number that sounds decisive and turns out, on inspection, to be the author's
impression wearing a percentage sign.

Here is what I actually check, what I have not measured, and why the distinction
is worth keeping.

---

## Criteria fixed in advance

Written before the setup was believed to be working, because a criterion invented
afterwards will always be satisfied.

| Criterion | How it is checked | Why this one |
|---|---|---|
| **A skill fires without being asked** | It activated on the first natural request in its domain, in the working language | If this fails, nothing else matters — the knowledge exists and is never reached |
| **Repeated explanations stop** | Compared against the baseline week's note, same duration | The original complaint. If it persists, the instruction layer is wrong |
| **A decision survives a month** | Ask about a month-old choice; the recorded reasoning comes back, not a fresh improvisation | This is the difference between memory and a diary |
| **Refusals actually happen** | The log contains rejections with reasons, not only acceptances | An intake that accepts everything has stopped adjudicating |
| **The always-loaded layer stays bounded** | Line count at each review | Growth here is silent and compounds |

---

## What these criteria caught

**The trigger language failure.** A skill written with an English description did
not fire on requests phrased in the operator's working language. No error, no
warning — the skill simply never activated, and the reasonable conclusion would
have been "this approach does not work." The criterion caught it because it asks
whether the skill fired, not whether the skill is good.

Fix: trigger vocabulary written in the language the operator actually types in,
plus an explicit *not for* clause. This is now the first thing I check on any new
skill, and it is [ADR-0002](../../decisions/0002-knowledge-lives-outside-the-always-loaded-file.md)'s
main recorded consequence.

**The always-loaded file at 149 lines.** A ceiling of 60 had been set one day
earlier, in writing, by me. The next day I proposed appending routing rules to
that same file. The rule caught the author of the rule, which is the only real
test of a rule.

**A gate applied to an unverified premise.** I argued against building a
protection on the grounds that the thing it protected did not exist yet. The
premise was wrong — it did exist, in a form I had not checked. The gate was
sound; the input was not. The correction recorded was not "be more careful" but a
change to the gate itself: *verifying that the thing is actually absent is part
of the gate, not a step before it.*

That last one is the most useful thing in this document. A criterion that only
ever confirms the setup is working is not a criterion.

---

## What I have not measured

**No controlled comparison.** I have not run the same tasks with and without this
setup on a fixed set of cases and compared outcomes. That is the experiment that
would justify a percentage, and it does not exist.

**No regression rate.** I do not know how often the setup causes a worse outcome
than no setup — for instance a skill firing on something it should have left
alone. I know it has happened; I do not know how often.

**No cost delta.** The session cost logger records consumption, but I have not
attributed the difference to the setup versus to the tasks themselves changing.

**No count of prevented bad decisions.** The most valuable number and the least
measurable. Every method has this problem and most publications quietly solve it
by not mentioning it.

---

## Why the gaps are stated rather than filled with adjectives

Two reasons, one principled and one practical.

**Principled.** This repository argues that unverified material must not be
presented as fact — that is the rule the intake skill enforces on everyone else's
writing. Applying it to my own claims is not modesty, it is consistency. A
document that demands provenance from articles and offers none for itself is
making an argument it does not believe.

**Practical.** An engineer reading a setup write-up is looking for the place
where the author stopped being careful. Give them a percentage with no experiment
behind it and they will find it in ten seconds, and everything else becomes
suspect. State the gap and the rest of the document holds.

---

## What would move this to a real measurement

Concrete and not yet done:

1. **A fixed case set** — thirty to forty representative requests, written once,
   reused. Enough variety that a single lucky configuration cannot pass them all.
2. **Two runs per case**, with and without the setup, on the same model version.
3. **Judged against pre-written criteria**, not read and rated by impression.
4. **Recorded per run:** outcome, cost, whether a skill fired that should not have.

This is a day of work and it is the honest next step. Until it exists, what I
have is a decision log, production behaviour and criteria fixed in advance —
which is more than most and less than proof.

---

**Related:** [07 · Installation order](07-installation-order.md) — where the
baseline week comes from · [Decision log](../../decisions/README.md)
