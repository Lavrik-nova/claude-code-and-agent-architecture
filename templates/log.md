# Intake log

One row per item. Appended on every `/learn` run. **Never rewritten.**

This file exists so the store can be audited without reading the store. It is
also where refusals live — and refusals are the majority of the entries, which is
the point.

<!--
  TEMPLATE.

  Append-only is not a style preference. If rows can be edited, the file cannot
  be trusted, and being trusted is its only function.

  Verdict vocabulary — keep it small and mean it:
    taken in full        the whole item became knowledge
    taken, one pattern   most of it was noise; one thing survived
    rejected             nothing survived; the reason column says why
    correction           it contradicted an existing page; note which won

  Delete these comments before use.
-->

| Date | Material | Verdict | Where it went |
|---|---|---|---|
| 2026-08-04 | <author> — <title> | taken in full | `wiki/agent-system-layers.md` (new) |
| 2026-08-04 | <author> — roundup of tooling | rejected; 3 items kept as candidates | `wiki/tooling-candidates.md` (marked unverified) |
| 2026-08-07 | <author> — <title> | taken, one pattern | `wiki/harness-components.md` → "two layers" |
| 2026-08-08 | <author> — <title> | rejected | — vendor material restating a known pattern |

---

## Operational notes

Free-form, dated, append-only. Decisions that are not architecture records but
that a future reader will need: what was deferred and on what condition, what
broke, what was measured.

- 2026-08-08 — third-party skill approved but **not installed**; entry condition
  is the production release shipping with green health checks. Detail in
  `decisions/0004-...`.
