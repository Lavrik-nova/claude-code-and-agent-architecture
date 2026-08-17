# 05 · Before you build an agent

> An agent is a loop with tools. Almost everything that determines whether it
> works sits outside the loop. This is the checklist I complete before writing
> the first line of one — and before adding a second agent to an existing system.

---

## Part 1 · The three layers, and which one is broken

Agent systems have three layers. Diagnosing the wrong one is the most expensive
mistake available, because each layer's failures are cheap to fix at its own
level and nearly impossible to fix from another.

| Layer | What it is | Symptom when it is the problem |
|---|---|---|
| **Harness** | Everything around the model: context assembly, tools, permissions, sandbox, memory, recovery | The model is capable but under-informed, over-permitted, or unable to resume |
| **Loop** | The cycle itself: call, tool request, result, repeat | Runs forever, stops too early, repeats an identical action, loses the goal |
| **Graph** | How multiple agents are wired together | Agents contradict each other, duplicate work, or wait on each other |

**A sixty-second diagnostic.** Give the failing task to a person with exactly the
context the agent had.

- The person also fails → **harness**. The information was not there.
- The person succeeds easily → **loop**. The information was there and was lost,
  or the cycle terminated wrong.
- The person succeeds but says "who was supposed to do this?" → **graph**.

Most teams reach for the graph first, because adding an agent feels like
progress. It is the most expensive of the three to change and the least likely to
be the cause.

---

## Part 2 · Pre-flight checklist

Nine questions. An unanswered question is not a blocker in itself — an
*unnoticed* unanswered question is.

### 1. What is the deterministic baseline?

What does the dumbest possible version do — a script, a lookup table, a query?
Build that first and record its result. Without a baseline, "the agent works" is
untestable, and you will not know whether the agent beat the script.

### 2. What is the unit of success?

Not "helpfulness." A specific, checkable outcome: the ticket was routed to the
right queue; the invoice matched; the test suite passed without the tests being
edited. If success cannot be checked without a human reading the output and
forming an impression, you are not ready to build.

### 3. What does the agent read, and who decided?

Retrieval is where most quality lives. Sending everything is expensive and
*worse* — the relevant material gets diluted. Selecting well is typically a
tenfold reduction in cost and an improvement in output at the same time. Decide
the selection policy before the loop exists.

### 4. What can it reach, and with what privileges?

Enumerate the tools, and for each: what it can read, what it can change, and
whether the change is reversible. Anything irreversible needs a real control, not
an instruction — see [04 · Safety gates](05-gates.md).

### 5. What happens when it stops halfway?

Every action is in one of four states: not started, started, completed,
completed-but-the-result-was-lost. The fourth is the one that hurts. Decide now
which actions must be idempotent, and how a resumed run knows what already
happened.

### 6. What are the ceilings, and what happens at the ceiling?

Maximum model calls, tool calls, delegation depth, wall-clock time, money. An
abort is a defined outcome with a defined handoff — decide in advance what the
user sees. An abort with no plan is a crash with extra steps.

### 7. Which memory failures are safe to swallow?

This one is routinely got wrong by applying a single rule everywhere.

- **Fail-open** is correct for personalization. A missing preference degrades the
  experience; it does not corrupt anything.
- **Fail-closed** is required for authorization, tenant isolation, idempotency
  keys, and mandatory policy. If the system cannot confirm the boundary, it must
  stop rather than proceed permissively.

Sort every memory read into one of these two before writing the fallback.

### 8. What will you look at when it misbehaves?

If you cannot reconstruct a single run — what was retrieved, what was called,
what came back, what it cost — you are not debugging, you are guessing. Decide
what a trace contains, and decide what must never appear in one.

### 9. Does this need to be built at all?

The last question and the one most worth asking out loud. Name the **outcome**
that changes if this exists. If the answer is a capability rather than an
outcome — "then it could also…" — it is not ready to be built.

Once an assistant can maintain a system, the marginal cost of one more automated
process looks like zero, so the system grows until nobody can say what any part
of it is for. Rebuilding something that already works buys ownership and costs
you the maintenance a vendor was absorbing. Sometimes that trade is right. It is
never automatically right.

---

## Part 3 · Before adding a second agent

A second agent is the most commonly-reached-for solution and one of the least
often correct. It is justified when one of these is true, and not otherwise:

- **Context isolation.** The subtask requires reading far more than the main
  agent should carry, and only a short conclusion needs to come back. This is the
  strongest reason and often the only real one.
- **Genuinely independent perspectives**, where the value comes from the
  perspectives being uncorrelated — an adversarial reviewer, not a second worker.
- **Parallelism over independent items**, where the items truly do not interact.

Not justified by: the stages being conceptually separate; the diagram looking
cleaner; wanting a specialist persona per task. Those produce coordination cost
with no capability gain.

**What a second agent adds automatically:** a way for two components to hold
contradictory beliefs, a new place for work to be duplicated, and a new way to
deadlock. Budget for those before you add it.

---

## Part 4 · How you will know

Fix these before you start, so you cannot grade your own homework afterwards.

| Question | Recorded before building |
|---|---|
| What did the deterministic baseline score? | |
| What is the target, and on what set of cases? | |
| What regression would make you roll this back? | |
| What does one resolved request cost? | |
| Who reviews the failures, and how often? | |

An empty column here is not a formality. It is the difference between a system
you can defend and a demonstration you can perform.

---

**Related:** [03 · Setup](07-installation-order.md) · [04 · Safety gates](05-gates.md) ·
[Decision log](../../decisions/README.md)
