# ADR-0003 · Do not add a second code-intelligence index

**Status:** Rejected proposal · **Date:** 2026-08-07

## Context

A well-regarded open-source tool builds a knowledge graph over a codebase:
symbols become nodes, nodes cluster by content, and questions resolve along a
short path instead of by rereading files. It also ingests non-code material —
PDFs, screenshots, audio — into the same graph.

It was recommended in a roundup of tools that fix common assistant weaknesses,
and the recommendation was sound in general. The setup already had a code
intelligence index in place, installed months earlier and in daily use.

## Options

**A. Install alongside the existing index.** Rejected. Two indexes over the same
code means two sources of truth, two staleness windows, and a standing question
at every lookup about which one to trust. The cost is not the installation; it is
that every future query carries a small decision.

**B. Replace the existing index.** Rejected. The incumbent is integrated,
familiar, and working. Replacing a working component requires evidence of a
specific failure it cannot address. No such failure had occurred.

**C. Install it for non-code material only** — the one capability the incumbent
lacks. Rejected on inspection: the documents in question were already covered by
a full-text-searchable store and a curated wiki, both consulted regularly.
The gap was theoretical.

**D. Decline. Record the evaluation.** **Chosen.**

## Decision

Not installed. Recorded in the tooling notes with the reasoning, so the same
recommendation does not get re-evaluated from scratch the next time it appears in
a roundup — which it will.

## Consequences

- The one genuine gap remains open: no graph over screenshots and PDFs. Accepted
  knowingly, not overlooked.
- The evaluation itself is now an asset. Re-deciding a settled question is a
  recurring and invisible cost, and a two-line note in the log removes it.

## Revisit when

A retrieval failure occurs over non-code material that full-text search
demonstrably cannot resolve. A specific failed lookup, not a general sense that
a graph would be nicer.

## Note on the general pattern

Adopting a tool because it is good is not the same as adopting it because it
solves a problem you have. In a setup that already has an answer for the same
job, the correct default is **decline and record**. The recording is what stops
the question from consuming attention every quarter.
