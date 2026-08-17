# 01 · The problem

A manufacturer in Québec. A catalogue in the hundreds of items, sold direct and
through retailers. A bilingual customer base that switches between French and
English mid-sentence, and a lifetime warranty that generates a steady flow of
claims about products bought years ago.

The inbound flow arrives through a widget on the website and a messaging channel.
It is not a support queue in the software sense — there is no ticket taxonomy, no
priority field, and no customer who has read the documentation.

---

## What the flow actually contains

The categories below are the ones that recur. None of them is exotic; the
difficulty is that they arrive mixed, in one sentence, from someone who does not
know which category they are in.

| Class | Example shape |
|---|---|
| **Product selection** | "I need something for a 15-inch laptop and a lunch box, for a teenager, that will survive a winter" |
| **Availability and where to buy** | "Do you have this in navy, and is there a store near me" |
| **Warranty** | "The zipper failed. I bought it maybe four years ago, no receipt" |
| **Repair and parts** | "Can this be fixed or do I replace it" |
| **Specification** | "Will it fit a 17-inch? What are the actual dimensions" |
| **Mixed** | Any two of the above in one message, with the second one implied |

The last row is the one that decides the architecture. A message that contains a
selection question *and* an availability constraint *and* an unstated condition —
the teenager, the winter — is the normal case, not the edge case.

---

## Volume and what it displaces

> **[NUMBER PENDING]** — share of widget conversations resolved without a human.
> This figure comes from the operator and will be stated with its measurement
> window and method. Nothing is published here until it is.

The economic shape is worth stating even before the number, because it is what
makes the project worth building rather than interesting.

**Without the widget, this flow becomes phone calls.** Every one of those
questions still gets asked — the customer simply asks it by telephone, or does
not ask it and buys elsewhere. Both outcomes are expensive and only one of them
is visible.

- A phone call about dimensions or warranty eligibility occupies a staff member
  for several minutes, including the interruption cost on whatever they were
  doing. Across a working week this is tens of hours.
- The calls that do not happen are worse. A customer who cannot get an answer at
  the moment they are deciding does not file a complaint; they close the tab.
  That loss never appears in any system, which is precisely why it is
  underweighted.

So the metric that matters is not "messages answered." It is **conversations that
would otherwise have become a phone call or a lost sale, and did not.**

---

## Why this is not a support-ticket problem

Three properties make the standard playbook inapplicable.

**The catalogue is physical and changes.** Products are discontinued, colours
come and go, specifications are corrected. The answer to "do you have this" has a
shelf life measured in days, and an agent confidently reciting a discontinued
item is worse than an agent that says it needs to check.

**Warranty is a judgment, not a lookup.** Eligibility depends on the failure
mode, the age, the usage and the product line. A rule table gets you part of the
way; the rest requires reading what the customer actually described, which is
usually not in warranty vocabulary.

**Language is not a setting.** A Québec customer writes French without
diacritics, switches to English for a technical term, and answers "ok" in a way
that carries no language signal at all. Getting this wrong is not a cosmetic
failure — replying in the wrong language reads as a machine, and the conversation
ends.

---

## The requirement, stated once

> Answer correctly, in the customer's language, from facts the business actually
> holds — and when those conditions cannot be met, say so and hand over, rather
> than produce a fluent answer that is wrong.

The second half is the expensive half. A system that always answers is easy. A
system that knows when it should not answer requires most of the architecture in
this section.

---

**Next:** [02 · Why a scripted bot fails here](02-why-scripts-fail.md)
