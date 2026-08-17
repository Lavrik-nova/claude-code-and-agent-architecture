# 04 · Product knowledge

Where the facts come from, how they are retrieved, and why the most important
line in this document is a `WHERE` clause.

---

## Two stores, different jobs

| Store | Holds | Nature |
|---|---|---|
| **Reasoning knowledge** | Pages of governed product and policy knowledge, keyword-indexed, reviewed and activated individually | Curated, slow-changing |
| **Operational memory** | Items learned from operation — patterns, corrections, lessons — typed and scoped per channel | Accumulating, reviewed before use |

They are separate because their failure modes are opposite. Curated knowledge
fails by going stale. Learned memory fails by admitting something wrong and then
repeating it confidently. Merging them means applying one review discipline to
two problems, and losing on both.

---

## The retrieval, and the line that matters

```python
def retrieve(query: str, channel: str = "both", limit: int = MAX_PAGES_INJECTED) -> list:
    """Return the most relevant APPROVED + ACTIVE pages for this channel.

    The review/activation filter lives in the SQL, so no caller can bypass it.
    Scoring is deterministic keyword overlap — no model call, no network."""
    ensure_wiki_tables()
    qtok = _tokens(query)
    rows = db.get_conn().execute(
        "SELECT * FROM wiki_pages"
        " WHERE review_status = ? AND activation_state = ?"
        "   AND (channel_scope = 'both' OR channel_scope = ?)",
        (REVIEW_APPROVED, STATE_ACTIVE, channel),
    ).fetchall()
    ...
```

### Why the filter is in the SQL and not in the caller

This is the single design decision in the project I would defend hardest.

The obvious implementation is to fetch pages and let the calling code filter out
the unapproved ones. It works. It is also a rule that exists only as long as
every caller remembers it — including callers that do not exist yet, written by
someone who has never read this file, under time pressure, at the end of a
release.

Putting the filter inside the query makes the unapproved page **unreachable**.
There is no code path that returns it, because there is no code path that selects
it. The comment in the source says exactly this: *so no caller can bypass it.*

The generalization: telling *a future programmer* not to do something is also a
request. A control is something the system cannot do.

### What this filter does not cover

A reviewer pushed on this and the push was correct. Putting the predicate in the
query closes exactly one hole — the caller who forgets. It does not close:

- **Direct SQL** from a script or a console session
- **A second retrieval path** written later without the predicate
- **A read replica or an export** consumed by something downstream
- **An administrative interface** that legitimately needs unapproved rows

There is currently **no test asserting that a new query against this table
carries the predicate.** That test is the thing that would turn a good habit into
an invariant, and it does not exist. Until it does, this is one enforced path
rather than a schema-level guarantee — a distinction worth keeping, since the
whole argument for putting the filter in SQL was that habits do not survive
contact with a deadline.

The stronger version is a view that exposes only approved and active rows, with
the base table reachable only by the administrative path. That is a migration,
not an edit, and it is on the list rather than done.

### Why scoring is deterministic

Keyword overlap, sorted by overlap then recency. No embedding call, no network,
no model.

Three consequences, all of them wanted:

- **It cannot fail.** A retrieval step that makes a network call has an outage
  mode; this one does not.
- **It is explainable.** When a wrong page is retrieved, the reason is visible in
  the keywords, and the fix is a data edit rather than a tuning exercise.
- **It is replaceable.** The scoring function is isolated. An embedding backend
  can replace it later without touching a single caller — which is the correct
  time to add one: when a specific retrieval failure demands it, not before.

At the current scale — a curated set of pages over a catalogue in the hundreds —
keyword overlap plus review discipline outperforms a vector store that nobody has
tuned, and it is inspectable by a person who is not an engineer.

---

## Channel scoping, and the exclusion that is easy to miss

Both stores carry a `channel_scope`, and the operational memory carries something
sharper: **certain memory types are excluded from certain channels entirely.**

```python
excluded_types = _CHANNEL_EXCLUDED_TYPES.get(channel, frozenset())
...
if r["mem_type"] in excluded_types:
    continue  # e.g. case_pattern is excluded from the widget channel
```

A pattern learned from handling an individual case is useful when a staff member
is looking at that class of case. It is **not** appropriate to surface to an
anonymous visitor on a public website, where it can leak the shape of somebody
else's situation.

The exclusion is by type at the query level, not by a judgment at answer time.
Same reasoning as the SQL filter: a boundary that depends on the right decision
being made every time is not a boundary.

---

## Storage is unbounded; injection is not

```python
"""Storage is unbounded; `limit` bounds only what is injected into a prompt."""
```

A one-line docstring holding an important distinction.

**Remembering** everything is nearly free and occasionally decisive — the item
that explains a rare failure is worth keeping for years. **Injecting** everything
is expensive and actively harmful, because irrelevant context dilutes relevant
context.

Systems that conflate the two end up deleting knowledge to save tokens, which is
the wrong trade made for a real reason. Keep the store; bound the injection.

---

## What happens when a fact is missing

The sufficiency check in [03](03-reasoning-stack.md) catches this before
generation. What happens next is decided by the governing principle card's
`uncertainty_rule`, not by a global setting — because "I do not have this" means
something different for a stock question than for a warranty question.

The one outcome that is never available: filling the gap from general knowledge.
A plausible-sounding specification the business does not actually hold is the
most expensive kind of wrong answer, because it is indistinguishable from a
correct one until a customer acts on it.

---

**Next:** [05 · Memory that stays oriented](05-memory-and-updates.md)
