# Claude Code, configured — and the agent it produced

**Part I** is what I load into Claude Code before I build anything: the code
graph, the critic subagents, the harness, and the three memory layers that keep
knowledge from evaporating between sessions.

**Part II** is the system I built with it — an AI administrator handling the
inbound flow for a manufacturer in Québec with a catalogue in the hundreds. Not
a scripted bot. A reasoning stack.

For engineers building agents with Claude Code. It assumes you already know what
a harness is and skips the explanation.

[![Licence: MIT](https://img.shields.io/badge/Licence-MIT-black.svg)](LICENSE)
[![Français](https://img.shields.io/badge/Aussi%20en-fran%C3%A7ais-0b7285.svg)](https://github.com/Lavrik-nova/architecture-agent-et-claude-code)

---

## Part I · The configured assistant

What is actually installed, why each piece is there, and what I rejected.

| # | Document | |
|---|---|---|
| 1 | [What is loaded, and why](docs/part1/01-what-is-loaded.md) | The full inventory: MCP code graph, plugins, skills, four hook points, the gated pipeline, three memory layers |
| 2 | [The context economy](docs/part1/02-context-economy.md) | Three loading tiers and the placement rule. Where a rule goes and what it costs to keep it there |
| 3 | [Memory architecture](docs/part1/03-memory-architecture.md) | Intake → adjudication → wiki → index → append-only log. Why refusal is the primary output |
| 4 | [The adjudication skill](docs/part1/04-adjudication-skill.md) | The complete working skill, not a description of one |
| 5 | [Gates](docs/part1/05-gates.md) | Reversible versus not. Where a real control lives, and where a gate is theatre |
| 6 | [How I know it works](docs/part1/06-how-i-know-it-works.md) | Observable criteria fixed in advance — and what I have not measured |
| 7 | [Installation order](docs/part1/07-installation-order.md) | Five stages. The first is a week of doing nothing, on purpose |
| 8 | [Before you build an agent](docs/part1/08-before-you-build.md) | Nine questions I answer before writing the first line of one — and before adding a second |

## Part II · The AI administrator

An anonymized architecture study. No client name, no product names, no prompts
reproduced verbatim — structure and intent in full, wording replaced with a
neutral example.

| # | Document | |
|---|---|---|
| 1 | [The problem](docs/part2/01-the-problem.md) | The inbound flow, its shape, and what it costs when a human absorbs it |
| 2 | [Why a scripted bot fails here](docs/part2/02-why-scripts-fail.md) | Four classes of real message that break every decision tree |
| 3 | [The reasoning stack](docs/part2/03-reasoning-stack.md) | Layer by layer: language lock, intent, principle cards, fact selection, sufficiency check, escalation |
| 4 | [Product knowledge](docs/part2/04-product-knowledge.md) | How facts are stored, retrieved, reviewed and activated — and why the filter lives in SQL |
| 5 | [Memory that stays oriented](docs/part2/05-memory-and-updates.md) | How the knowledge base updates itself and how the agent stays correct while it does |
| 6 | [Limits and handoff](docs/part2/06-limits-and-handoff.md) | When the system must stop, and why admitting ignorance is worth paying for |
| 7 | [Catching a bad answer](docs/part2/07-catching-bad-answers.md) | What is recorded per exchange, what raises an alarm, how a failure is dissected |
| 8 | [What is deliberately absent](docs/part2/08-deliberately-absent.md) | Rejected designs, with reasons |

## Also here

- **[Decision log](decisions/README.md)** — every architectural choice with the
  options that lost and why.
- **[Templates](templates/)** — the instruction file, wiki page, index, log,
  adjudication skill and decision record. Copy them.

---

## One thing that runs through both parts

> Telling a model not to do something is a request. It is not a control.

In Part I this is why the permission and gate design looks the way it does. In
Part II it is why the review filter on the knowledge base sits inside the SQL
query rather than in the prompt — so that no caller can bypass it, including a
future version of the code written by someone who has forgotten the rule.

The same sentence produced both designs. That is most of what this repository is
about.

---

## What is not here

No controlled comparison of this setup against no setup. I have a decision log,
production behaviour, and criteria fixed in advance — not an experiment. Where a
number appears, its source is stated. Where I have not measured something, it
says so.

No client name, no product names, no credentials, no customer data in any form,
including anonymized samples.

## Licence

[MIT](LICENSE).
