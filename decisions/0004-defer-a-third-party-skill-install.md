# ADR-0004 · Approve a third-party skill, but defer installation behind a release window

**Status:** Accepted, deferred pending entry condition · **Date:** 2026-08-08

## Context

A third-party skill adds a capability the assistant lacks natively and that
closes a real gap in intake. It is MIT-licensed, widely adopted, and processes
material locally — data leaves the machine only in one identifiable case, and
that case is avoidable by policy.

Three things were true at the same time on the machine in question:

1. The skill would run with the account's full privileges — which reach
   production credentials, deployment tokens, a live database, and customer
   records. Third-party code inherits the account's blast radius, and no
   configuration file in the assistant reduces it.
2. The source had **not been read**. Licence and adoption figures had been
   checked. That is not an audit.
3. A production release with a paid measurement step and two customer-affecting
   fixes was scheduled in the same window.

## Options

**A. Install now.** Rejected on sequencing. Two independent changes in one window
destroy attribution: if anything breaks the next morning, the cause is
undeterminable, and the investigation costs more than the tool saves.

**B. Decline permanently.** Rejected. The capability gap is real and the risk is
manageable with ordinary measures.

**C. Restrict the skill's file access through the assistant's permission
configuration.** Rejected as **ineffective, not merely insufficient**. Those rules
gate the assistant's own tool calls; they do not constrain a child process. Once
a script executes, it reads whatever the account can read. Presenting this as a
mitigation would have been worse than doing nothing, because it would have
retired the question.

**D. True isolation — separate OS account or virtual machine.** Genuinely
effective. Rejected for this case: it removes the skill from the main working
session, which is the entire reason to install it. Kept on the shelf for higher-
risk tooling.

**E. Approve, defer behind an explicit entry condition, and read the source
first.** **Chosen.**

## Decision

Approved with three conditions, all of which must hold before installation:

1. The production release ships and health checks are green. Installation does
   not share a window with a deployment.
2. The skill's scripts are read line by line and the findings reported. Not a
   guarantee — a real check rather than a proxy for one.
3. Consumption ceilings are fixed **in advance**, not observed afterwards:
   bounded extraction, intermediate output written to disk, only a digest
   admitted to context, and a hard token ceiling per item.

An earlier draft of the acceptance criterion — "it should not cost more than
processing a comparable document" — was discarded. The comparison was unsound:
this material is systematically longer than the comparison class, so the
criterion would have failed for reasons unrelated to the tool.

## Consequences

- The capability gap stays open until the release ships. Accepted; the ordering
  is the control.
- The source review is real work that has to happen before the value is
  available, and it may find nothing. That is the normal outcome of diligence and
  is not a reason to skip it.
- The deferral is recorded with its entry condition, so it resumes on the
  condition rather than on someone remembering.

## Revisit when

The entry condition is met. If it is still unmet after four weeks, that is
information about priorities and the decision is reopened rather than left
hanging.

## Note on language

Throughout this record the wording is **"not audited,"** never "no telemetry
found." Licence and adoption are evidence of popularity, not of safety, and
describing them as an audit is how a team comes to believe it performed diligence
it never performed. The phrasing is the control.
