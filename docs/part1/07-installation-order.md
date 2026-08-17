# 07 · Installation order — five stages

Each stage depends on the one before it. Running them out of order produces a
setup that looks complete and cannot be evaluated.

Total hands-on time is about three hours, spread across a week that starts with
doing nothing.

---

## Stage 0 · Baseline — one week, passive

**Do not configure anything yet.**

Work normally for a week and keep a running note of what actually goes wrong.
Not what you imagine will go wrong — what does. Typical entries:

```
Mon  re-explained the deployment target for the third time
Tue  flattened a repository-pattern call into a direct query, twice
Wed  asked four clarifying questions to rename one variable
Thu  lost the reasoning behind Monday's schema decision
```

Two reasons this stage is not optional.

**You cannot measure an improvement without a before.** Every claim you make
later — to yourself, to a client, to a hiring manager — rests on this note or it
rests on nothing.

**Half of your imagined problems will not appear.** Configuration written against
imagined problems is the single largest source of bloat in these setups, and it
is unusually hard to remove later because nobody can prove it is useless.

---

## Stage 1 · The project spec — 45 minutes

Claude Code reads `CLAUDE.md` at the start of every session. Two layers belong
there, and most setups conflate them.

**The philosophy layer** — general engineering principles. Shared across
projects, rarely changes, and is genuinely portable.

**The spec layer** — this repository's commands, architectural constraints and
conventions. Not portable. Not guessable. This is the part that is usually
missing, and it is the part that pays.

### Procedure

1. Run `/init` and let it generate a starting file from the actual repository.
2. Delete everything that is not a command, an architectural constraint, or a
   convention.
3. Apply the admission test from
   [01 · The context economy](02-context-economy.md) to each surviving line.
4. Stop at 60 lines. If you are over, you are describing preferences.

### A spec layer that earns its place

```markdown
## Commands
- Test:  npm run test:unit
- Lint:  npm run lint   (never fix style manually — lint owns it)

## Architecture
- Repository pattern lives in /src/repositories.
  Services do not query the database directly.
- /src/legacy is frozen. Flag before touching; never refactor opportunistically.

## Conventions
- Every new endpoint gets a schema in /src/schemas before the handler.
- Ask before adding a dependency.
```

Twelve lines. Every one is checkable, and every violation is expensive. The last
line does the most work in practice: it is specific, it is testable, and it is a
rule rather than a sentiment.

**What does not belong here:** anything a reader would describe as advice.

Full annotated version: [templates/CLAUDE.md](../../templates/CLAUDE.md).

---

## Stage 2 · The knowledge store — 15 minutes

```
knowledge/
├── inbox/          material arrives here; not yet knowledge
│   └── links.md    one URL per line, for things that are not files
├── wiki/           dense pages with provenance
├── archive/        originals after processing — moved, never deleted
├── index.md        one line per page. The only file read routinely.
└── log.md          append-only: what was taken, what was refused, why
```

Create it empty. An empty store with the right shape beats a full store with the
wrong one, because the shape determines what you are willing to put in it.

Two conventions to fix now, while it is cheap:

- **`index.md` holds one line per page and nothing else.** The moment content
  starts living in the index, you have rebuilt the always-loaded tier.
- **`log.md` is append-only.** It is the audit trail. If it can be edited, it
  cannot be trusted, and its only purpose is to be trusted.

Templates: [wiki-index.md](../../templates/wiki-index.md) ·
[wiki-page.md](../../templates/wiki-page.md) · [log.md](../../templates/log.md).

---

## Stage 3 · Adjudication — 30 minutes

Install a skill that owns the intake loop. Complete working version:
[templates/skills/learn/SKILL.md](../../templates/skills/learn/SKILL.md).

The skill encodes the three outcomes from
[02 · Memory architecture](03-memory-architecture.md) and, critically, requires
that refusals be stated by name.

### The first run is a calibration run

Feed it five real items. Expect three or four rejections. If everything is
accepted, the skill is being agreeable rather than useful — tighten the wording
until it refuses things, and read its stated reasons rather than its verdicts.

Two details that matter more than they appear:

- **Write the trigger words in the language you actually type in.** A skill
  described only in English will not reliably fire on a request written in
  another language, and it fails silently.
- **Set `disable-model-invocation: true` for skills that must only run when you
  ask.** Intake is one of them: it moves files and edits durable pages, and it
  should never start on its own initiative.

---

## Stage 4 · Gates — one hour

Sort every action the assistant can take into two boxes: **reversible** and
**not**. Then put real controls on the second box.

This is its own document — see [04 · Safety gates](05-gates.md) — but the
part that belongs in setup is the sorting exercise. Do it on paper, once,
explicitly. Most people have never written the list down and are surprised by
how short the irreversible one is, and by what is on it.

---

## Stage 5 · Measurement — 30 minutes

Before you believe the setup is working, write down what working looks like.
Do it now, while you are still capable of being disappointed.

**Bad criterion:** "responses feel more consistent."
**Bad criterion:** "the assistant seems to remember more."

Both are unfalsifiable, and both will feel true regardless of the outcome.

**Workable criteria** are observable and were fixed in advance:

| Criterion | How you check it |
|---|---|
| A skill fires without being asked | It activated on the first natural request in its domain |
| Repeated explanations stop | Compare against the Stage 0 note, same week length |
| A decision survives a month | Ask about a month-old choice; the recorded reasoning comes back, not a fresh guess |
| Refusals actually happen | `log.md` contains rejections with reasons, not only acceptances |

If a criterion fails, the honest first hypothesis is that the *trigger* is wrong,
not that the idea is wrong. Descriptions are the most common point of failure and
the cheapest to fix.

---

## What I deliberately left out

**A hook that classifies every request with a model.** Latency and cost on every
turn, plus a component that can fail on every turn, to do what a description
already does. See [01 · The context economy](02-context-economy.md).

**A vector store.** For a personal or single-team knowledge base measured in
dozens of pages, an index file and full-text search are sufficient and
inspectable. Retrieval infrastructure should appear when a demonstrated retrieval
failure calls for it, and not one stage earlier.

**Automatic self-editing memory.** An assistant that rewrites its own durable
knowledge without review is not a more mature system — it is a system with a
different, quieter failure mode. Human review of durable writes is cheap at this
scale, and it is where most of the value gets caught.

---

**Next:** [04 · Safety gates](05-gates.md) ·
[05 · Before you build an agent](08-before-you-build.md)
