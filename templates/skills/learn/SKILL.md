---
name: learn
description: Process material collected in knowledge/inbox and fold what is worth keeping into the wiki. Adjudicate each item, reject most of it out loud, and record every verdict.
disable-model-invocation: true
---

<!--
  TEMPLATE · the adjudication skill. This is the complete working version.

  disable-model-invocation: true is deliberate. This skill moves files and edits
  durable pages. It runs when asked and never on its own initiative.

  If you want a skill that DOES fire on its own, remove that line and write the
  description as a trigger specification — the words and situations that mean
  "read the rest of this" — in the language you actually type in. A description
  in the wrong language fails silently: no error, the skill simply never
  activates and you conclude the idea does not work.

  Delete these comments before use.
-->

# /learn

Material arrives in `knowledge/inbox` — articles, exports, transcripts, and URLs
in `inbox/links.md`. This turns some of it into knowledge and refuses the rest.

## For each item

### 1 · Read it, then classify honestly

- **A working pattern** — someone ran this in production and said how, including
  what constrains it.
- **A useful correction** — it contradicts something already in the wiki.
- **Nothing** — a rehash, a listicle, vendor material, or already covered.

### 2 · If it is nothing, say so and add nothing

Name the file and give one line of reason. Out loud, in the report, not silently.

A knowledge base that accepts everything is a landfill, and a landfill is worse
than an empty folder because it looks like an asset. **Rejecting is the main
work, not a failure.** Expect roughly two-thirds of good intake to end here.

### 3 · If it is worth keeping, write it into the wiki

Prefer **appending to an existing page** over creating a new one. Few thick pages
beat many thin ones: related constraints that live apart contradict each other
silently.

Keep only what is actionable — the pattern, the constraints that make it safe,
recoverable and observable, and **where it does not apply**. Not a summary of the
article. Two paragraphs is usually enough.

Every entry carries:

```
source:      author + where published
as_of:       the date of the material, not today
source_type: primary | secondary
```

`primary` — official documentation, the author's own repository or writing, a
production write-up with evidence.
`secondary` — someone's retelling.
**Secondary material never settles a decision on its own.**

If a claim contradicts an existing page, do not silently overwrite. Keep both,
mark the older one superseded, and state which won and why.

### 4 · Move the original to `archive/`

Moved, never deleted. A disputed claim has to be traceable to its source.

### 5 · Update `index.md`

One line per page, grouped by the question it answers, with the date. The index
is a pointer file. If content starts living there, stop and move it back.

## Topics beyond engineering

Sort by topic yourself. Add a new section to `index.md` when a genuinely new area
appears; do not force unrelated material into an existing page. The same rules
apply everywhere — a working pattern, not a summary, and refusals stated out loud.

## Report back — always these three columns

| Material | Verdict | Where |

Then one line per rejected item saying why. Then the page count. Nothing else.

**Append one row per item to `knowledge/log.md`.** Never rewrite that file — it
is how the store gets audited without reading the store.

## Say when knowledge is used

When a wiki page shapes a decision during ordinary work, name it in one line:
`from wiki/<page>`. Knowledge that is stored but never visibly used cannot be
evaluated, and an unevaluated knowledge base quietly stops being maintained.

**If the repository contradicts a page, the repository wins.** Say so, and fix
the page in the same breath.

## Also applies

If you find something valuable while researching on your own — same procedure.
Record the source and the fact that it was found rather than submitted.
