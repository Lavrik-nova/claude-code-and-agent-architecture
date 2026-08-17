# Engineering knowledge index

One line per page. Read a full page only when working in that topic.
Maintained by the `/learn` skill.

<!--
  TEMPLATE.

  This file is a pointer, not a document. The moment content starts living here,
  you have rebuilt the always-loaded tier at a higher price.

  Each line: name, link, what the page actually answers, date last touched.
  The summary should be specific enough that you can tell from the index alone
  whether the page is worth opening. "Notes on memory" is not; the examples
  below are.

  Group by question, not by source. A reader arrives with a problem, not with
  an author in mind.

  Delete these comments before use.
-->

## Diagnosis — what exactly is broken

- `agent-system-layers` — harness / loop / graph;
  the sixty-second test for which layer failed; five ways to break a system
  invisibly · 2026-08-04
- `harness-components` — the loop, plans against
  context rot, files as external memory, subagents, permissions and sandbox,
  checkpoints versus durable memory · 2026-08-07

## Memory and knowledge

- `memory-patterns` — memory layers and their risk
  profile, context selection under budget, the deterministic wall, choosing a
  store, safe writes · 2026-08-04
- `observability` — tracing one request, what to log,
  what to alert on, privacy of traces · 2026-08-04

## Safety and autonomy

- `tool-permissions` — reach and privilege,
  idempotency, action ceilings · 2026-08-04
- `human-in-the-loop` — where a human is genuinely
  required and where the gate is theatre; the risk matrix · 2026-08-04

## Quality and operations

- `eval-architecture` — five levels of checking, what
  to record per run, why a single score is useless · 2026-08-04
- `cost-governance` — budgets, routing by risk, cost per
  resolved request, the gate before building anything new · 2026-08-08
