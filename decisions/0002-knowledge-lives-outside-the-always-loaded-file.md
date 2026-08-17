# ADR-0002 · Durable knowledge lives in a wiki reachable by trigger, not in the always-loaded file

**Status:** Accepted · **Date:** 2026-08-08 · **Builds on:** [ADR-0001](0001-two-layer-claude-md.md)

## Context

Two capabilities were being added to the setup, each with policy attached — which
mode to use by default, what must never be sent outward, when to prefer one route
over another. The policy had to be present when it was needed, without the owner
having to remember to invoke it.

The obvious move was to append the routing rules to the always-loaded
instruction file. That file was already 149 lines, well past the 60-line ceiling
established in ADR-0001 — so the obvious move directly contradicted a decision
taken the previous day.

The contradiction is worth recording rather than quietly fixing. It is the
default failure mode of every knowledge system: the always-loaded tier is the
easiest place to put something, so everything ends up there, one reasonable
addition at a time.

## Options

**A. Append the rules to the always-loaded file.** Simple, immediate, always
present. Rejected: it grows the most expensive tier for content needed on a small
fraction of requests, and it violates a decision made one day earlier — which
would have made that decision decorative.

**B. Put the policy in a hook that classifies each request and injects context.**
Rejected: latency and cost on every turn, plus a new failure point on every turn,
to accomplish what a skill description already does inside the existing request.
Classification does not belong in the hot path.

**C. Encode the trigger in a skill description; keep the policy in the skill
body and the reasoning in the wiki.** **Chosen.** The description is one to three
lines and always loaded; the body loads only when the trigger fires; the wiki
holds the rationale and is read only when the topic is live.

**D. A new skill per capability.** Rejected: each new skill is a new always-loaded
description and a new file to maintain. Existing skills already owned the
relevant domains — extending them cost nothing structurally.

## Decision

Three tiers, with an explicit placement rule:

> If you need to know a rule in order to realize it is time to read it, it goes
> in the description.
> If you only need it once you have already started, it goes in the body.

Nothing was added to the always-loaded file. One exception is reserved for
invariants — rules that are checkable and whose violation is irreversible — and
they are added individually, never as a block.

## Consequences

- The knowledge store can grow without bound at no recurring cost. Ten pages cost
  ten index lines, not ten pages.
- New dependency: **skill descriptions become load-bearing**. A description that
  does not match how the owner actually phrases things means the skill never
  fires, and it fails silently — no error, just absence. Descriptions must be
  written in the language the owner types in, and verified rather than assumed.
- Rationale is now one hop away instead of inline. Acceptable: rationale is
  needed when a decision is being reconsidered, which is rare and deliberate.

## Revisit when

- A skill measurably fails to fire on natural phrasing more than twice → the
  problem is the description, and the trigger vocabulary needs work before
  anything else is blamed.
- The always-loaded file grows past 60 lines again → run a review pass rather
  than raising the ceiling.
