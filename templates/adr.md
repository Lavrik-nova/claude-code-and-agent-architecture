# ADR-NNNN · <Decision, stated as a sentence with a verb>

**Status:** Proposed | Accepted | Accepted, deferred | Rejected proposal | Superseded by ADR-NNNN
**Date:** YYYY-MM-DD
**Builds on / supersedes:** <links, or omit>

<!--
  TEMPLATE · architecture decision record.

  Write one before any change that is expensive to reverse: a dependency that
  will spread, a data model, an authorization boundary, a third-party component,
  a change to how memory or retrieval works.

  Do not write one for reversible work. An ADR per commit trains everyone to
  ignore the folder.

  The title is a sentence with a verb — "Split the instruction file into two
  layers", not "Instruction file structure". A title without a verb is a topic,
  and a topic is not a decision.

  Delete these comments before use.
-->

## Context

What was true at the moment the decision became necessary. Forces, constraints,
what had already been tried. Written so that a reader arriving in a year — or a
reader who is you, having forgotten — can reconstruct the situation without
asking anyone.

State the uncomfortable parts. If a decision was made under time pressure, with
incomplete information, or against a prior commitment, that belongs here. It is
the most useful thing in the record.

## Options

Every option that was genuinely considered, each with the reason it was or was
not chosen.

**A. <Option>.** <One line on what it is.> Rejected: <why>.

**B. <Option>.** <One line.> Rejected: <why> — and specifically whether it was
rejected as *ineffective* (it does not do what it appears to do) or as
*insufficient* (it works, but not enough). Those are different, and conflating
them is how a non-control gets recorded as a mitigation.

**C. <Option>.** **Chosen.**

A record with one option is not a decision record. It is an announcement with a
template around it.

## Decision

What was chosen, stated so that someone could act on it without reading the rest
of the document. Include the specific thresholds, ceilings or phrasings that were
fixed — a decision with the numbers left out will be re-argued.

## Consequences

What this costs. What it makes harder. What new dependency it introduces.

The honest version includes the things that are worse now. Every real decision
has some, and a record listing only benefits is marketing.

## Revisit when

The condition that reopens this. A specific, observable trigger — not "in six
months" and not "if it stops working."

Without this line, a decision becomes folklore: everyone follows it, nobody
remembers why, and it outlives the situation that justified it.

## Evidence

What the decision rests on, and how strong that is. If it rests on a single
self-reported result, say so and treat it as a direction rather than a
measurement. If the source is secondary, state what would make it primary.
