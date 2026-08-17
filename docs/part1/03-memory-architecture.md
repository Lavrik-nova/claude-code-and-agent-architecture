# 03 · Memory architecture

> The hard part of a knowledge system is not writing things down. It is refusing
> to write most things down, and being able to defend each refusal.

## The loop

```
inbox/  →  adjudication  →  wiki/  →  index.md
   │            │                        │
   │            └──→ log.md ←────────────┘
   │                (append-only)
   └──→ archive/   (originals, never deleted)
```

Five moving parts. Each one exists because leaving it out causes a specific,
observed failure.

| Part | Purpose | Failure if omitted |
|---|---|---|
| `inbox/` | Staging. Material arrives here and is not yet knowledge. | Raw material gets pasted straight into durable pages; provenance is lost within a week. |
| Adjudication | A judgment call, made once, recorded. | Everything is kept. The store grows and its average quality falls until nobody consults it. |
| `wiki/` | Dense, provenance-tagged pages. Few pages, thick. | Many thin pages. Related constraints scatter and contradict each other silently. |
| `index.md` | One line per page. The only part read routinely. | The store cannot be consulted cheaply, so it is not consulted. |
| `log.md` | Append-only. What was taken, what was refused, why. | No way to audit the store without reading all of it. |
| `archive/` | Originals, moved not deleted. | A disputed claim cannot be traced back to its source. |

## Adjudication: three outcomes, not two

Every item in the inbox resolves to exactly one of these.

**A working pattern.** Someone ran this in production and said how. It carries
constraints — what makes it safe, recoverable, observable — and it says where it
does *not* apply. Keep it.

**A useful correction.** It contradicts something already in the store. Keep it,
and do not silently overwrite the old page. Mark the superseded claim, state
which won, and state why. The reasoning is worth more than the conclusion,
because the next contradiction will need it.

**Nothing.** A rehash, a listicle, vendor material, or a restatement of something
already held. **Say so by name, in one line, and add nothing.**

That third outcome is the load-bearing one. Two-thirds of good intake ends there.
A store that never rejects is not curated, and the effort of curation is exactly
what makes the remaining third trustworthy.

## Provenance: three fields, non-negotiable

Every page and every entry carries:

```yaml
source:      author + where it was published
as_of:       the date of the material, not the date you filed it
source_type: primary | secondary
```

`primary` — official documentation, the author's own repository or writing, a
production write-up with evidence attached.
`secondary` — someone's retelling of any of the above.

**Secondary material never settles a decision on its own.** It can raise a
hypothesis, point at a primary source, or corroborate. It cannot be the reason
you changed an architecture. This single rule eliminates most of the damage that
confident blog posts do to a knowledge base.

`as_of` matters more than it looks. A pattern that was correct in one model
generation is often scaffolding built around a limitation that no longer exists.
Undated knowledge cannot be aged out, so it never is.

## Typing knowledge: four kinds, not one

"Only record confirmed facts" sounds disciplined and quietly destroys value. An
unconfirmed observation is not worthless — it is worth exactly as much as an
unconfirmed observation, provided it is labelled as one.

| Type | Definition | Allowed to drive a decision? |
|---|---|---|
| `observation` | Something seen once. No mechanism proposed. | No |
| `hypothesis` | A proposed mechanism, not yet tested. | Only to design a test |
| `confirmed` | Verified against the system itself, or a primary source. | Yes |
| `policy` | A decision that has been made and now binds. | Yes, it *is* the decision |

The rule is that an unconfirmed item may never be *presented* as a fact — not
that it must be thrown away. Discarding hypotheses means rediscovering them.

## Why not one growing knowledge file

The single append-only `KNOWLEDGE.md` is the most common design and it fails
predictably.

- It is either loaded always (Tier 1 — and it grows without bound) or never
  (in which case it is a diary).
- Append-only with no structure means contradictions accumulate silently.
  Line 40 and line 900 disagree; nothing surfaces the conflict.
- There is no unit to supersede, date, or retire, so nothing ever is.

The fix is not a database. It is *pages with provenance*, *an index that is cheap
to read*, and *a log that records refusals*. Plain markdown in the repository
survives; documentation systems that live elsewhere do not.

## Where the boundary sits

Knowledge lives in the repository. **Customer data does not go into the knowledge
store, in any form, at any stage** — not as an example, not anonymized, not "just
for one page." Patterns extracted from working with customer data are knowledge;
the data is not, and the two are easy to conflate at 11 p.m.

If material in the inbox contains personal or customer data, it is processed
under the rules for that data — which are stricter than these — and only the
pattern crosses into the store.

---

**Related:** [01 · The context economy](02-context-economy.md) ·
[04 · Safety gates](05-gates.md) ·
[templates/wiki-page.md](../../templates/wiki-page.md)
