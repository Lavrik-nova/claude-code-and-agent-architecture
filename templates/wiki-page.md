# <Topic — phrased as the question the page answers>

`source: <author, where published>` · `as_of: <date of the material, not today>` · `source_type: primary | secondary`

<!--
  TEMPLATE · a knowledge page.

  Prefer appending to an existing page over creating a new one. Few thick pages
  beat many thin ones: related constraints that live apart contradict each other
  silently.

  Two paragraphs per pattern is usually enough. This is not a summary of an
  article — it is the pattern, its constraints, and where it does not apply.

  source_type
    primary   — official docs, the author's own repo or writing, a production
                write-up with evidence attached
    secondary — someone's retelling
  Secondary material never settles a decision on its own.

  Delete these comments before use.
-->

One or two sentences on what problem this page exists to solve, and how it
relates to the neighbouring pages. Link them: `other-page`.

## The pattern

What was actually done, concretely enough to act on. Name the mechanism, not the
category — "a queue" is a category; "an outbox table written in the same
transaction as the state change" is a mechanism.

## Constraints that make it safe

What has to be true for this to work. Typically some combination of:

- **Recoverable** — what happens on a partial failure, and how a resumed run
  knows what already completed.
- **Observable** — what you look at when it misbehaves, and what must never
  appear in that trace.
- **Bounded** — the ceiling, and the defined outcome when the ceiling is hit.
- **Reversible** — how to undo it, or an explicit statement that you cannot.

## Where it does not apply

The section most often skipped and most often the reason a pattern fails in a new
setting. Name the conditions under which this is the wrong choice. A pattern with
no stated limits has not been understood yet.

## Contradictions

If this supersedes an earlier claim, state both, say which won and why. Do not
silently overwrite — the reasoning is worth more than the conclusion, because the
next contradiction will need it.

Format:

> **Superseded, <date>.** Earlier claim: <…>. Current claim: <…>.
> Reason the current claim won: <…>.

## Evidence and its limits

What kind of support this rests on, stated plainly. If the numbers are
self-reported by one author, say so and treat them as a direction. If the source
is secondary, say what would make it primary.
