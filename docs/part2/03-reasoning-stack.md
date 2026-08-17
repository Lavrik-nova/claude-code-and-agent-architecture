# 03 · The reasoning stack

Six layers between an incoming message and a sent answer. Each one can stop the
sequence, and stopping is a designed outcome rather than a failure.

The ordering is deliberate: **the cheapest and most deterministic checks run
first.** By the time a model is involved, the language is fixed, the intent is
known, and the set of admissible facts has already been narrowed.

```mermaid
flowchart TD
    M["Incoming message"] --> L1["1 · Language lock<br/><i>deterministic, no model call</i>"]
    L1 --> L2["2 · Intent classification"]
    L2 --> L3["3 · Principle cards<br/><i>which rules govern this</i>"]
    L3 --> L4["4 · Fact selection<br/><i>only what those rules need</i>"]
    L4 --> L5{"5 · Sufficiency check"}
    L5 -->|"enough"| L6["6 · Generation<br/><i>bounded by the cards</i>"]
    L5 -->|"not enough"| E["Escalation<br/><i>or a stated gap</i>"]
    L6 --> V{"Post-check"}
    V -->|"passes"| OUT["Answer sent"]
    V -->|"fails"| E

    style L1 fill:#e7f5ff,stroke:#1971c2
    style L5 fill:#fff4e6,stroke:#e8590c,stroke-width:2px
    style E fill:#f3f0ff,stroke:#6741d9
```

---

## Layer 1 · Language lock

Runs first, **with no model call at all.** Scoring is arithmetic over the raw
text.

```python
def resolve(message: str, session_language: str) -> str:
    """The language THIS answer must be written in.

    A clear signal wins over the session — a client writing French is asking
    for French. An unclear message keeps the session, which is what stops the
    language from flapping on "oui" or "ok".
    """
    current = session_language or FR
    clear = detect(message)
    return clear or current
```

Three design decisions are visible in eight lines.

**Accents count double.** They are decisive and cheap — a single accented
character is stronger evidence than several ambiguous words.

**Accent-less Québec French is scored explicitly.** Real customers write `sac a
dos`, `boite a lunch`, `garantie a vie`. A detector built on accents alone reads
these as English and answers in the wrong language. The phrase list exists
because the failure was observed, not because it was anticipated.

**Ambiguous input keeps the session language.** A message with no clear signal —
`ok`, `merci`, a bare model number — does not get to change anything. Without
this rule the language flaps mid-conversation, which is the single most
machine-like thing a bilingual agent can do.

**Why deterministic.** Language is checkable, cheap and high-frequency. Spending
a model call on it would add latency to every turn and introduce variance into
something that has a right answer. This is the same principle as
[Part I · Gates](../part1/05-gates.md): put the decision in code where code can
decide it.

---

## Layer 2 · Intent classification

Maps the message onto a small set of intents and tags. This is where the "two
questions in one sentence" case from
[02](02-why-scripts-fail.md) stops being a branch selection problem: the output
is a set, not a choice.

The tags produced here are what layer 4 uses to pull in facts that no principle
explicitly requested but that the topic clearly touches.

---

## Layer 3 · Principle cards — the part that makes it think

This is the core structure. Twenty-five cards, each one a small governed rule
with twelve fields:

```jsonc
{
  "id": "…",
  "title": "…",
  "source": "where this rule came from",
  "approval_status": "approved | draft",
  "channel_scope": "widget | messaging | both",
  "triggers": ["what activates this card"],
  "known_facts": ["fact ids this rule depends on"],
  "allowed_action": "what may be done under this rule",
  "forbidden_action": "what may never be done under it",
  "uncertainty_rule": "what to do when the inputs are insufficient",
  "next_step_class": "what kind of move comes next",
  "exceptions": ["where this rule does not hold"]
}
```

Four of those fields are the difference between a rule set and a reasoning
structure.

**`forbidden_action`.** Stating what is permitted is not enough — a model given
only permissions will find adjacent behaviour that was never considered.
Prohibitions are carried explicitly, per rule, next to the permission.

**`uncertainty_rule`.** *Per card, not global.* What to do with insufficient
information is different for a warranty question than for a stock question:
one must not guess at all, the other may offer a qualified answer. A single
global confidence threshold cannot express that, which is why systems that use
one end up either over-cautious everywhere or over-confident everywhere.

**`exceptions`.** Where the rule does not hold. The limits are where the failures
live — and **only 7 of the 25 cards currently carry one.** The other eighteen
were written as rules without stated limits, which by my own argument means they
have not been fully thought through. The field exists and is under-used. That is
a backlog item, not a design.

### The cards, in numbers

| | |
|---|---|
| Principle cards | 25 |
| Facts | 27 |
| Distinct `uncertainty_rule` values | 24 of 25 |
| Cards with a non-empty `forbidden_action` | 25 of 25 |
| Cards with stated `exceptions` | **7 of 25** |
| Approval states in use | `approved` (15), `approved_locked` (10) |
| Cards in draft | 0 |
| Channel scope | 20 all channels, 5 widget-only |
| Average triggers per card | 2.1 |

`approved_locked` is a second approval tier: cards that may not be edited without
an explicit unlock. It covers the rules where a wrong edit produces a commitment
the business has to honour — warranty language, and anything a customer would
reasonably act on.

Two numbers in that table are uncomfortable and both are left in. Seven of
twenty-five on `exceptions` is a gap. Zero drafts means the review step has never
actually rejected a card, which is either discipline upstream or a review that
has not yet been tested — I do not currently know which, and saying so is more
useful than picking the flattering reading.

**`approval_status` and `channel_scope`.** A card is not live because someone
wrote it. It is live because it was approved, and it applies to the channels it
was scoped to. See [04 · Product knowledge](04-product-knowledge.md) for where
that filter is enforced.

---

## Layer 4 · Fact selection

Twenty-seven facts exist. A given answer sees a handful.

```python
def select_facts(cards: list, intent: dict) -> list:
    """Only the facts the selected principles actually need, plus tag matches."""
    wanted = {fid for c in cards for fid in c.get("known_facts", [])}
    by_id = {f["id"]: f for f in facts()}
    out = [by_id[fid] for fid in sorted(wanted) if fid in by_id]
    have = {f["id"] for f in out}
    for f in facts():
        if f["id"] not in have and set(f.get("tags", [])) & set(intent["tags"]):
            out.append(f)
    return out
```

Two passes: the facts the active principles declared they need, then facts whose
tags match the intent even though no principle asked for them.

**Why not send everything.** Twenty-seven facts is small enough that dumping all
of them would fit. It is still wrong. Irrelevant material does not sit inertly in
a prompt — it dilutes the relevant material and gives the model permission to
reach for something that does not apply. Selection is a quality mechanism that
happens to also be cheaper.

This is [Part I · The context economy](../part1/02-context-economy.md) applied
one level down: the same argument that governs what loads into a session governs
what loads into a single answer.

---

## Layer 5 · The uncertainty contract — and an honest limit

This is the layer where I have to be precise, because an earlier draft of this
document overstated it and a reviewer caught it.

**What it is.** Every principle card carries an `uncertainty_rule`: what to do
when the inputs that rule depends on are not present. Ask one targeted question,
give a qualified partial answer, or hand over. It arrives with the card, scoped
to that rule, and it is data — reviewable, diffable, approved individually.

**How specific it actually is.** Twenty-five cards carry **24 distinct**
`uncertainty_rule` values. It is not a template repeated with the field filled
in; it is close to one deliberate decision per rule. Every card also carries a
non-empty `forbidden_action`.

**What it is not.** It is **not a deterministic gate.** There is no function in
this codebase that evaluates fact coverage and blocks generation. The contract is
expressed in data and carried out by the model.

That distinction matters and I am not going to blur it. The argument against a
post-generation confidence score — that a model rating its own output is the same
process judging itself — applies here too, only earlier and with better inputs.
Moving the question before generation and scoping it per rule narrows the blind
spot. It does not eliminate it.

**What would make it a real gate.** A deterministic coverage check: for the
active cards, are all `known_facts` present in the selected set? If not, the
`next_step_class` is forced without consulting the model at all. This is a small
function over data the system already holds — perhaps forty lines. It is not
written, and until it is, this layer is a strong contract rather than a control.

By the standard used throughout this repository — *a control must be code, a flag
or a test* — this layer does not yet qualify. Stating that is the point of
having the standard.

### What is deterministic today

For the avoidance of the same doubt, these are code and verifiable in the source:

| Mechanism | Deterministic |
|---|---|
| Language resolution (layer 1) | Yes — arithmetic, no model call |
| Fact selection (layer 4) | Yes — set operations over declared dependencies |
| Review/activation filter on retrieval | Yes — enforced in the SQL predicate |
| Channel exclusion of memory types | Yes — filtered at query level |
| Catalogue reconciliation halt on any error | Yes |
| Reply-status classification (per thread) | Yes |
| The uncertainty contract (layer 5) | **No — data contract, model-enforced** |

---

## Layer 6 · Generation, bounded

The model writes the answer. What it is allowed to write is already constrained
by the cards: the permitted action, the prohibitions, the tone, the facts it may
cite, and the class of next step it may propose.

The system prompt is not where the rules live. It is where the *voice* lives.
Rules live in the cards, and the cards are data — reviewable, diffable, approved
individually, and scoped per channel. A rule that lives in a prompt cannot be any
of those things.

---

## Post-check and the rule that produced it

After generation there is a verification pass before the answer is sent. Its
existence is the direct result of a rule the project learned the hard way, and
which is now written into the critic's own instructions:

> Never propose fixing behaviour by adding words to a prompt. On this project
> that has been rolled back three times; a control must be code, a flag or a
> test.

Three rollbacks. Each time, the fix that felt fastest — add a sentence to the
prompt telling it not to do that — held until the next unusual message and then
failed silently. What worked was moving the constraint into a place that does not
depend on the model's cooperation.

That sentence is the same one that governs Part I. It was learned here.

---

**Next:** [04 · Product knowledge](04-product-knowledge.md) — where the facts
come from and why the review filter lives in SQL.
