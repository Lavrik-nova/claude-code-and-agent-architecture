# Decision log

Architecture decision records for this setup. Each one states the context, the
options considered, the choice, and what it costs.

The rejected options are the point. A decision without them is an announcement.

| # | Decision | Status | Date |
|---|---|---|---|
| [0001](0001-two-layer-claude-md.md) | Split the instruction file into a philosophy layer and a project spec layer | Accepted | 2026-08-07 |
| [0002](0002-knowledge-lives-outside-the-always-loaded-file.md) | Durable knowledge lives in a wiki, reachable by trigger — not in the always-loaded file | Accepted | 2026-08-08 |
| [0003](0003-no-second-code-indexer.md) | Do not add a second code-intelligence index | Rejected proposal | 2026-08-07 |
| [0004](0004-defer-a-third-party-skill-install.md) | Approve a third-party skill, but defer installation behind a release window | Accepted, deferred | 2026-08-08 |

## Format

Short, five sections, no ceremony. Template: [../templates/adr.md](../templates/adr.md).

```
# ADR-NNNN · Title
Status · Date · Supersedes / superseded by
## Context      what was true when the decision was needed
## Options      what was considered, including what was rejected and why
## Decision     what was chosen
## Consequences what this costs, and what it makes harder
## Revisit when the condition that would reopen this
```

**`Revisit when` is not optional.** A decision without a reopening condition
becomes folklore: everyone follows it, nobody remembers why, and it outlives the
situation that justified it.
