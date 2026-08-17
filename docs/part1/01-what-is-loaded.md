# 01 · What is loaded, and why

The full inventory of what sits inside Claude Code before I open a project. Each
entry says what it does, what it replaced, and — where it applies — what I
rejected in its place.

Nothing here was installed because it was interesting. Every piece was added
after a specific failure I could name.

---

## Code intelligence — an MCP graph over the codebase

A knowledge graph of every symbol, edge and file in the working repository,
queried over MCP. On the production agent repository it currently holds:

```
120 files · 3,652 symbols · 6,844 edges · 5.2 MB · SQLite with FTS5 + WAL
```

**What it replaced.** Grep-and-read exploration. Answering "how does an incoming
message reach the answer generator" used to mean a dozen searches and reading
several files end to end. It is now two calls that return entry points, related
symbols and the relevant code together.

**Why it matters more than it sounds.** The cost is not the searching — it is
that every file read lands in the context window and stays there, crowding out
the actual task. A graph query returns the three functions that matter instead of
the six files that contain them.

**The rule I enforce on myself.** If the index exists in the working directory,
grep and whole-file reads for symbol lookup are forbidden. Index first, then read
the one place that matters. This is written into the instruction file because
without it the habit reverts within a week.

**What I rejected.** A second indexer, recommended in a tooling roundup and
genuinely good. Two indexes over the same code means two sources of truth and a
small decision at every lookup about which to trust. Recorded in
[ADR-0003](../../decisions/0003-no-second-code-indexer.md) so the question does
not get re-litigated the next time it appears in someone's newsletter.

---

## Plugins

| Plugin | What it does |
|---|---|
| **Cross-session memory** | Persists observations across sessions with its own search. Answers "did we already solve this?" without me reconstructing it |
| **Safety director** *(mine, local)* | Enforces the two-mode protocol below. Not published — it encodes decisions specific to this machine |
| **Messaging bridge** | Reaches the operator outside the terminal for long-running work |

The safety plugin being local and hand-written is the point. The general-purpose
ones handle general problems; the rules about *this* machine, *these*
credentials and *this* production system do not generalize and should not be
outsourced to someone else's defaults.

---

## Skills — the trigger layer

Three personal skills, always available. What matters is not that they exist but
**what is loaded into context to make them work**: only the description, one to
three lines. The body loads when a trigger fires and costs nothing when idle.

| Skill | Fires on |
|---|---|
| **Knowledge intake** | Explicit invocation only. It moves files and edits durable pages, so it never starts on its own initiative |
| **Research to implementation** | Costly architecture choices — memory, retrieval, evaluation, orchestration. Recall a proven pattern before inventing one |
| **Heuristics lens** | Manual. A source of hypotheses for reviewing a plan, explicitly not an authority |

**A detail that decides whether any of this works.** The trigger words are
written in the language the operator actually types in. A skill described only in
English does not reliably fire on a request written in French or Ukrainian, and
the failure is silent — no error, the skill simply never activates and everyone
concludes the idea was bad. Every description here carries its trigger vocabulary
in the working language, plus an explicit *not for* clause so a broadly worded
skill does not fire on everything.

---

## The gated pipeline — where a human sits in the loop

Three commands that form a state machine with a human in the transition:

```
client-discovery  →  [ human approval ]  →  run-build
   DISCOVERY                                    BUILD
```

Intake reads raw material and produces a structured brief and a proposal, then
**stops**. It cannot advance itself. Approval is a separate command that only a
person runs; it validates the state and merges the staging work into production.
Only then can the build phase start.

**Why a state machine rather than a rule.** "Ask before building" in an
instruction file is a request, and requests are followed until the moment they
are inconvenient. A state machine cannot be talked out of it: the build command
reads the approved contract or it has nothing to read.

---

## Hooks — four lifecycle points

Hooks are the only mechanism here that the model cannot argue with. They run
deterministically, outside the conversation.

| Point | What runs |
|---|---|
| **Before a write** | Guard script. Checks the target before anything is written |
| **After a write** | Capture script, feeding the memory layer |
| **On stop** | Two: a discipline reminder, and a cost logger recording what the session consumed |
| **On session start** | Reports the state of the knowledge base and whether unprocessed material is waiting |

**The session-start hook is the smallest and the most valuable.** It reads the
knowledge index and says, in one line, how large it is and how many items are
sitting unprocessed. Without it the intake folder silently fills up, because the
person who filled it is busy and the assistant has no reason to look.

**What I deliberately do not run as a hook:** a model call that classifies every
incoming request to decide what context to inject. It adds latency, cost and a
new failure point to every single turn, in exchange for something a skill
description already does inside the existing request. Hooks are for
deterministic checks. Judgment belongs in the conversation.

---

## Memory — three layers, different jobs

| Layer | Holds | Read |
|---|---|---|
| **Instruction file** | Invariants: commands, architectural constraints, boundaries | Every session, always |
| **Operational memory** | Session logs, profile, decisions, open loops. SQLite with full-text search | On demand, and a warm-start file at session open |
| **Engineering wiki** | Adjudicated knowledge, provenance-tagged. An index of one line per page | Index at session start; a page only when working in its topic |

The separation is the whole design. Conflating them is the standard failure: one
growing knowledge file that is either loaded always — and therefore unbounded in
cost — or never, in which case it is a diary.

Detail in [03 · Memory architecture](03-memory-architecture.md).

---

## Two-mode protocol

Every action is reversible or it is not.

**Reversible** proceeds without confirmation: reading, analysis, drafting,
editing inside the project, local version control.

**Irreversible or outward-facing** stops and waits: pushing, deployment,
recursive delete, CI changes, sending anything outward, writing outside the
project, anything touching production credentials or data.

The stop is not a polite question. It requires an explicit authorization phrase
that cannot be produced by reflex — `yes`, `ok` and `go ahead` are what people
type while thinking about something else.

Detail in [05 · Gates](05-gates.md).

---

## A gap I have not closed

The permission configuration currently holds **357 allow rules and no deny
rules.** It is an allow-list that grew by accretion, one prompt at a time, which
is exactly the accumulation pattern this repository warns about elsewhere.

It is not dangerous on its own — the two-mode protocol and the write guard sit in
front of the operations that matter. But it is not the design I would defend, and
a deny-list for credential paths and out-of-project writes is the next piece of
work rather than something already done.

I am recording it here rather than omitting it. A setup document that lists only
the parts that went well is a brochure.

---

**Next:** [02 · The context economy](02-context-economy.md) — how these pieces
are kept from crowding each other out.
