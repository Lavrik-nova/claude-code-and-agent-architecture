# 09 · What the measurement found

Two external reviewers read this repository before you did. Both asked the same
question: where are the numbers. Here they are, with what they do not prove.

---

## The project's first number

**121 real questions, frozen.** Taken from actual traffic, fixed once, replayed
against the release running in production. 242 requests under a ceiling of 300.
Zero technical errors. Every request tagged and kept in the dialogue log, so the
run is replayable.

**Four independent judges**, each with fresh context and the rubric. **None of
them saw the run that produced the answers.** An agent that grades itself always
passes — the same process that answered is doing the judging, and it cannot see
its own blind spot.

```
substantive on the first turn ......... 76 / 121  =  62.8 %

of the 45 misses:
   33  "check the website" for what the website does not show
    9  a fact the knowledge base holds and did not surface
    3  wrong

price and promo codes ................. 2 / 23   =  8.7 %
availability and where to buy ......... 10 / 19
product specifications ................ 26 / 34
warranty .............................. 11 / 13
order and delivery .................... 20 / 21
```

**Second axis — factual correctness**, scored only where a confirmed source
settles it: 37 correct, 9 wrong, 50 stated confidently with no source, 25
undetermined. **Where it is settled at all: 80 % correct.**

All nine errors have the same shape. **The knowledge base holds the answer and
the reply contradicts it.** That is not a knowledge gap; it is a surfacing
defect — repairable, and repairable in a known place.

---

## The most useful number is the worst one

**Price and promo codes: 8.7 %.** Two acceptable answers out of twenty-three.

It is the weakest category by a wide margin, and it is the question a customer
asks immediately before buying. No average would have shown it: at 62.8 %
overall, a dashboard would have read "improving" while the pre-purchase question
failed nine times out of ten.

That is what a per-category measurement is for. A single overall score measures
the comfort of whoever published it.

---

## Why 8.7 % — and what that number actually measures

It does not measure the model. It measures the state of information inside the
business.

**Of the 45 misses, 33 are "check the website" for what the website does not
show.** The agent was not wrong. It pointed at the authoritative source, and the
source does not contain the answer. No amount of better reasoning fixes that.

Price is the worst category because it is the fact that lives in the most places
at once: the product page, a promotional campaign, the retailer list, an
exception granted over the phone. None of these openly contradict each other —
the versions simply coexist, each true somewhere.

The same defect shows up elsewhere in the measurement: **two live instruction
files said the opposite of each other, with no precedence rule.** The judges
tripped on it. The model trips on it the same way, silently.

### The principle, for anyone designing these systems

> **An agent cannot be more coherent than the organization it speaks for.**
> It does not create facts. It redistributes the ones that exist — and where the
> business has no single authoritative answer, it reproduces the ambiguity at
> scale, faster than a person would.

What reasoning can fix and what it cannot:

| Cause of the miss | Fixable on the agent side |
|---|---|
| The fact exists, retrieval missed it | Yes — retrieval, keywords |
| The fact exists, the reply contradicts it | Yes — a deterministic control |
| Two live sources contradict each other | **No** — the business needs a precedence rule |
| The fact is published nowhere | **No** — that is a documentation project |

Two rows out of four are not engineering problems.

### What this changes before day one

Before quoting a project, five questions per class of fact — price, availability,
specifications, warranty:

1. Is there **one** authoritative source, or several coexisting?
2. Is it machine-readable, or readable only by an experienced employee?
3. Who owns it, by name?
4. How often does it change, and who finds out?
5. When two sources disagree, which wins — and is that written down?

If the answer to the first question is "it depends who you ask", the project
begins with documentation work, and **that has to appear as a separate phase in
the proposal.** Absorbed into development it becomes invisible: the disorder
stays the client's and the failure becomes the supplier's.

It is the most transferable lesson in the whole measurement, and it has nothing
to do with AI.

---

## The judges were wrong, and the error is on the record

Five verdicts called a support mailbox shown in chat a factual error, citing two
instruction files. A more recent owner decision, written into the code, says the
opposite.

The five verdicts were **reclassified and the count moved from 15 errors to 9**,
with the correction recorded rather than quietly applied.

What matters is not the correction. It is what the correction exposed: **the
instruction corpus still forbids what the owner decision permits, and the model
reads both.** The judges tripped on it in the open. The model trips on it
silently, in the customer's direction.

Two live sources, no precedence rule. That is the real defect, and it surfaced
only because somebody refused to erase an inconvenient mistake.

---

## What this number does not prove

Written here so it cannot be quoted louder than I said it.

- **One run**, at a fixed temperature. Another run gives another number.
- **A model judge**, with no hand-checked sample. The judges have already been
  wrong once — it is documented above.
- **No baseline.** Nobody measured what these 121 questions produced *without*
  the agent. Without that, 62.8 % is comparable only to another run of this same
  set under this same rubric.

**It is a reference point, not a verdict.** That distinction is the point of the
document.

---

## The rollback, with the hour and the line number

A configuration flag was supposed to **extend** the chat context. It **replaced**
it. Two lines in the entry file.

What silently vanished from the production prompt:

| Lost | Volume |
|---|---|
| Active business rules | 4 |
| Lessons learned from operation | 14, six of them from the previous 31 days |
| Unified memory layer | 390 rows |
| Language and persistent-memory instructions | 2 files |
| "Last contact" note | present for 171 of 171 customers |
| Tone guide, including a correction approved the day before | 1 |
| Conversation history | 6 turns instead of 10 |

**Nothing crashed.** The chat answered, politely, with an amputated context.

**Verified against the deployed image, with line numbers — not against the
branch.** The branch says what someone meant to write. The image says what is
running in front of the customer. On this project the two have already diverged.

Three customer classes changed behaviour: an English-speaking customer, a
returning customer, and any rule added in the last month. **None of the three was
in the six-case smoke test.** Six cases passing green while three real classes
degraded.

**Flag switched off immediately**, next release, health confirmed. The second
flag stayed on with its reason written down — in that configuration the block is
appended to the full context and nothing is lost — together with the admission
that **that exact configuration had never been in the live smoke test either.**

### The three rules this incident produced

**Verify against what is deployed.** Not the branch, not the intent.

**A smoke test that does not cover customer classes measures nothing.** Six green
cases escorted a real loss into production.

**A silent failure is worse than a loud one.** Nothing fell over. That is exactly
why it lasted.

---

## The critic stopped a layer whose tests were all green

The most uncomfortable of the three, and the most instructive.

A new reasoning layer passed everything: **44 of 44 assertions**, **44 of 44 red
under mutation** — so the tests genuinely bite — and **byte-identical output on
12 of 12 inputs** with the flag off. By every criterion I had set, it was ready.

An adversarial subagent blocked it before the commit, with six defects.

> The tests were green and measuring the wrong thing: the fixture catalogue I
> assembled protected an implementation the live catalogue breaks.

Three of the claims were **verified by reading the code rather than taken on
report.** All three held:

- A product-family rule never fired, because the real catalogue names do not
  contain the word it keys on.
- An "is this a product question?" function already existed and was not being
  used. **Measured over 837 real messages: 796 would have received a doubt
  instruction, 645 of them off-topic.**
- A docstring claimed a price block was withheld in a particular case. It was
  not. The context would have carried "the only usable prices" and "give no
  price" at the same time.

Two more the critic found and I had not: prefix matching turned ordinary words
into product names, and the system inferred a product family from a colour
contained in a name — exactly the inference the owner had forbidden.

**My own test meant to forbid that inference could not see it: it read string
literals, not behaviour.**

### What the incident actually changed

The conclusion was not "fix the layer". It was that **the order of the stages is
wrong**: a product family cannot be read from how a catalogue row happens to be
named, because the names come from two sources with different spellings and
change within a week. The typed field has to come first.

And that conclusion **is not mine to make.** Both honest options were written up
with a recommendation and its reason, and the decision belongs to the owner of
the system.

---

## The wall was counting instead of blocking

Last, and shortest.

One answer sent a customer an address that a written decision had cancelled. The
corresponding rule was present among the blocking rules. It went out anyway.

The deterministic guard was running **in observe mode** in production. It scored
the violation instead of stopping it — it was counting the tail it had been built
to hold.

Fixing it changes what the customer sees. So it is an owner decision and a stage
of its own, not a side effect of another.

**The transferable lesson:** a control in observe mode is not a control, and the
dashboard cannot tell the difference. It shows green either way.

---

## Why this document exists

Two reviewers wrote, independently, that the repository said "production" without
ever showing a number or a dated incident. They were right, and the material
existed — in the project history, not in the shop window.

It is a common failure and worth naming: **we document what we understood, and
forget to document what we paid to understand.** The second one is worth more.

---

**See also:** [03 · The reasoning stack](03-reasoning-stack.md) ·
[07 · Catching a bad answer](07-catching-bad-answers.md) ·
[08 · What is deliberately absent](08-deliberately-absent.md)
