# 01 · The context economy

> Knowledge is not free to hold. It is free to *store* and expensive to *carry*.
> Almost every setup mistake I see comes from confusing the two.

## The three tiers

An assistant's working context has exactly three kinds of content, distinguished
by when they load.

### Tier 1 — always loaded, always paid

`CLAUDE.md` and equivalent project instruction files are read at the start of
every session and carried through every turn. A line here is charged on the
request where it is essential and on the four hundred requests where it is not.

**Admission test.** A rule belongs in Tier 1 only if it satisfies both:

1. **It is checkable.** Someone reading the output can say whether it was
   followed. "Ask before adding a new dependency" is checkable. "Write clean
   code" is not — it is a mood.
2. **Violating it is expensive or irreversible.** A convention that costs a
   minute to correct after the fact does not need to be carried on every request.

Anything failing either test goes to Tier 2 or 3. This usually cuts an existing
file by half on the first pass.

### Tier 2 — always loaded, but one to three lines

A skill's `description` field is loaded into context permanently; the skill's
**body is not**. That asymmetry is the mechanism the whole system runs on.

The description is not a summary. It is a **trigger specification**: the words,
phrases, and situations that mean "the body of this is now relevant." Write it in
the language the user actually types in. A description in English will not
reliably fire on a request written in French or Ukrainian, and the failure is
silent — the skill simply never activates and everyone concludes it does not work.

A useful description names both sides:

```yaml
description: >
  Knowledge for costly architecture choices in agent systems — memory,
  retrieval, evaluation, orchestration, reliability. Recall a proven pattern
  before inventing one.
  Not for: bug fixes, refactors, tests, docs, dependency bumps, tone edits.
```

The "not for" clause does real work. Without it, a broadly-worded skill fires on
everything and you have quietly rebuilt Tier 1 at a higher price.

### Tier 3 — loaded on demand, free when idle

Skill bodies, wiki pages, decision records, reference material. This tier can be
arbitrarily large. Ten thousand words of hard-won constraints cost nothing on the
requests that do not need them.

Almost everything belongs here. The instinct to promote a page to Tier 1 because
it is *important* is the instinct to fight. Importance is not the criterion —
**frequency of need** is.

## The placement rule

> If you need to know a rule in order to realize it is time to read it, it goes
> in the description.
> If you only need it once you have already started, it goes in the body.

Worked example. Policy for handling video material:

| Rule | Tier | Why |
|---|---|---|
| "A video link means run the video skill" | 2 — description | Without this you never open the body |
| "Default to scene-aware mode, cap at 100 frames" | 3 — body | Only relevant once you are already running it |
| "Transcript writes to disk; only a digest enters context" | 3 — body | Same |
| "Recordings containing customer data never leave the machine" | 1 — invariant | Checkable, and violating it is irreversible |

Four rules about one tool, distributed across three tiers. Putting all four in
Tier 1 is the common mistake and it costs about eight times as much for no gain.

## What this replaces

**Do not put an LLM classifier on every user request.** It is technically
possible to run a fast model on each incoming prompt to decide what context to
inject. It is a bad trade: latency on every turn, cost on every turn, and a new
component that can fail on every turn — to accomplish what a well-written
description does inside the existing request.

Reserve programmatic hooks for **deterministic** work: blocking a dangerous shell
pattern, running the test suite after an edit, checking that a decision record
accompanied a change to a sensitive directory. Deterministic checks in the hot
path are cheap and honest. Model calls in the hot path are neither.

## Reviewing the budget

Tier 1 grows by accretion. Nobody adds forty lines at once; everyone adds two,
eleven times. Put a recurring review on the calendar and apply one question to
each line:

> Would this rule survive being read aloud to a new colleague on their first day
> as a thing they must remember forever?

If not, it moves down a tier or it goes. The reviews are short. Skipping them is
how a 40-line file becomes a 150-line file that nobody reads carefully, including
the model.

---

**Related:** [02 · Memory architecture](03-memory-architecture.md) ·
[ADR-0001](../../decisions/0001-two-layer-claude-md.md) ·
[ADR-0002](../../decisions/0002-knowledge-lives-outside-the-always-loaded-file.md)
