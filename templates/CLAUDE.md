<!--
  TEMPLATE · the project spec layer.
  Generate a first draft with /init, then cut it against this file.

  Admission test for every line:
    1. Is it checkable — can a reader tell whether it was followed?
    2. Is violating it expensive or irreversible?
  Both must be true. Otherwise it belongs in a skill body or the wiki.

  Ceiling: 60 lines. Over that, you are describing preferences.
  Delete these comments before use.
-->

# <Project name>

## Commands

<!-- Exact, runnable. No prose. This is the section that pays for itself daily. -->

- Test:    `npm run test:unit`
- Test one:`npm run test:unit -- <path>`
- Lint:    `npm run lint`   — lint owns style; never fix style by hand
- Build:   `npm run build`
- Dev:     `npm run dev`    — port 3000

## Architecture

<!-- Only constraints a reader could violate without noticing. Each one names
     what is forbidden, not what is preferred. -->

- Repository pattern lives in `/src/repositories`. Services do not query the
  database directly.
- `/src/legacy` is frozen. Flag before touching. Never refactor opportunistically.
- Background jobs are defined in `/src/jobs` and registered in one place —
  `/src/jobs/index.ts`. A job that is not registered does not run, silently.

## Conventions

<!-- Rules, not sentiments. "Ask before X" is a rule. "Write clear code" is not. -->

- Every new endpoint gets a schema in `/src/schemas` before the handler exists.
- Ask before adding a dependency.
- Migrations are forward-only. No edits to a migration that has run anywhere.

## Boundaries

<!-- Irreversible or outward-facing actions. These stop and wait.
     Keep this list short and specific — a long list gets skimmed.
     Real enforcement belongs in hooks; this section states intent. -->

- Never push to a remote, deploy, or change CI configuration without explicit
  authorization in this exact phrase: `<pick an unambiguous phrase>`
- Never write outside this repository.
- Never send customer data to a third-party service, in any form, including
  anonymized samples.
- Credentials live in `.env` and are never read into context, quoted, or logged.

## Knowledge

<!-- One pointer. Never the content. -->

- Engineering knowledge: `knowledge/index.md`. Read the index; open a page only
  when working in that topic.
- Decisions: `decisions/`. Read the relevant record before reopening a settled
  choice.
