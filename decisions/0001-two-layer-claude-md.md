# ADR-0001 · Split the instruction file into a philosophy layer and a project spec layer

**Status:** Accepted · **Date:** 2026-08-07

## Context

`CLAUDE.md` is read at the start of every session, so its contents are a
recurring cost on every request. The widely circulated starting files are
collections of general engineering principles — ask before assuming, prefer the
simplest solution, do not touch unrelated code, flag uncertainty. They are
sensible and they are portable, which is precisely why they went viral.

Two problems surfaced in sustained use rather than in demos.

**General rules applied indiscriminately become a tax.** "Ask before assuming,"
applied to every task, turns a variable rename into an interview. The rule is
right for ambiguous, high-stakes changes and wrong for the large majority of
edits that are simply obvious.

**General rules override project-specific reasoning they cannot see.** "Prefer
the simplest solution" will flatten a repository-pattern call into a direct
query, because the extra layer looks like overhead to anyone who does not know
why it is there. Simpler to write; inconsistent with the forty other files doing
it the other way.

Separately, a philosophy file contains nothing about the test runner, the folder
conventions, or the modules that must not be touched — which is most of what an
instruction file is for.

## Options

**A. One general file.** Portable, immediately useful, requires no thought.
Rejected: it addresses roughly a third of what the file exists to do, and the
remaining two-thirds — the part that is unique to this repository and therefore
unguessable — stays missing.

**B. One project-specific file.** Everything tailored, nothing general. Rejected:
the general principles are genuinely useful and would be rewritten per project.

**C. Two layers.** A general layer, rarely changed, shared across projects; a
spec layer per repository containing commands, architectural constraints and
conventions. **Chosen.**

**D. Two layers plus a shared plugin distributing the general layer.** Correct at
several machines or several teams. Rejected for now: one owner, one machine —
the versioning and distribution mechanism is cost without a corresponding
problem. Reopen when the second machine appears.

## Decision

Two layers. The spec layer is generated from the real repository, then cut to
under 60 lines against a single admission test: **a rule stays only if it is
checkable and its violation is expensive.**

If a rule would not survive being read aloud to a new colleague on day one as
something they must remember permanently, it is cut or moved to a lower tier.

## Consequences

- Two files to maintain instead of one, and a judgment call each time a rule is
  added about which layer owns it.
- The general layer stops being able to justify itself by usefulness alone; it
  now has to earn a slot against the spec layer.
- The cut is uncomfortable. Removing a rule feels like losing knowledge, which is
  why the admission test is written down rather than applied by instinct.

## Revisit when

- A second machine or a second maintainer appears → reconsider option D.
- The spec layer exceeds 60 lines twice in a row after a review → the admission
  test is being applied too generously, or content belongs in a skill.

## Evidence

Direction supported by a published before-and-after over twelve pull requests
across four repositories: fewer clarifying round-trips, no further
architecture-flattening incidents, higher first-pass acceptance. Self-reported,
single author, not independently reproduced — treated as a direction, not a
metric. Recorded as `source_type: secondary`.
