# 04 · Safety gates

> Telling a model not to do something is a request. It is not a control.
> Every design in this document follows from that one sentence.

## The two boxes

Sort every action available to the assistant into one of two boxes. There is no
third box, and the ambiguous cases go in the second.

### Box A — reversible

Reading and analyzing files. Generating text, code, plans, drafts. Editing files
inside the project working tree. Version control operations that stay local:
`add`, `commit`, `status`, `diff`, `log`.

**These proceed without confirmation.** Confirmation prompts on reversible work
are the fastest way to train yourself to approve without reading, which is how
the prompts on the *other* box stop working.

### Box B — irreversible or outward-facing

- Pushing to a remote; force-push; anything with `--force` or `--no-verify`
- Recursive deletion; mass overwrite
- Changes to CI/CD or deployment configuration
- Sending anything outward: email, chat, pull request, API call to a third party
- Writing outside the project directory
- Anything that touches production data or credentials

**These stop and wait.** Not "ask nicely" — stop.

## The stop protocol

When an action falls in Box B, four steps, in order:

1. **Explain in plain language: what, why, and what the risk is.** Written for a
   decision-maker, not for an engineer. If the explanation requires the reader to
   already understand the system, it is not an explanation.
2. **Name the assets in scope, specifically.** Not "it will have file access" —
   name the credential file, the production database, the customer records. The
   abstract phrasing is what lets a bad decision feel fine.
3. **Recommend an outside review** for anything consequential. A second opinion
   from an independent system, on a screenshot, costs a few minutes.
4. **Wait for an explicit, unambiguous authorization phrase.**

That last point is not ceremony. `yes`, `ok`, `sure`, and `go ahead` are what
people type while thinking about something else. Pick a phrase that cannot be
produced by reflex, and honour only that phrase.

## Where the real controls live

A control that exists only as instruction text is a suggestion. Real controls
live in the harness.

| Control | Where | Use for |
|---|---|---|
| Deterministic pre-execution check | `PreToolUse` hook | Dangerous shell patterns, recursive delete, writes outside the project, outbound data, credential paths, deployment |
| Post-change verification | `PostToolUse` hook | Targeted tests, schema validation, lint after an edit |
| Completion check | `Stop` hook | If sensitive directories changed but no decision record or evaluation case was added, require it before finishing |
| Isolation | Sandbox / separate account | Anything running third-party code |

Three rules for hooks, learned the expensive way:

**Keep model calls out of the hot path.** A classifier on every user request adds
latency, cost, and a failure point to every turn. Hooks are for deterministic
checks. If a check requires judgment, it belongs in the conversation, not in the
pipeline.

**Global hooks guard; project hooks verify.** A machine-wide hook should only
block genuinely dangerous operations. Anything that needs to know how *this*
project works belongs to the project.

**Do not treat an experimental mechanism as a production gate.** If the
documentation calls a feature experimental, it may advise but must not be the
only thing standing between you and an irreversible action.

## Third-party code: what the controls actually cover

This deserves its own section because it is where the reasoning most often goes
soft.

When you install a third-party skill or plugin, **it executes with your account's
privileges.** Permission rules in the assistant's configuration gate *the
assistant's* tool calls. They do not constrain a child process. Once a script
runs, it can read anything your user account can read.

So the honest inventory before installing anything is: *what can my account
reach?* On a typical working machine that is production credentials, deployment
tokens, customer records, and every repository you have cloned. That is the
blast radius, and no configuration file reduces it.

**What does not work:**
- Deny-lists in the assistant's configuration — wrong layer.
- Star count and licence — evidence of popularity, not of safety.
- "It's open source" — true, and irrelevant unless someone read it.

**What does work:**
- Reading the scripts before the first run. Usually a few hundred lines. Not a
  guarantee, but it is a real check rather than a proxy for one.
- Genuine isolation: a separate account, a container, a virtual machine. Real,
  and it costs the convenience that made you want the tool.
- Where a service must be automated under an account, use a **dedicated account
  created for that purpose**, so the worst outcome is losing something empty.

**A phrasing discipline I hold myself to.** If I have not read the code, I write
"not audited," not "no telemetry found." Licence and popularity are not an audit,
and describing them as one is how a team ends up believing it did diligence it
never did. This is not pedantry — it is the difference between a decision made
with open eyes and one made with a comfortable phrase.

## Sequencing is a safety control

The least obvious gate, and the one most often skipped.

**Do not install new tooling in the same window as a production deployment.**
Not because installation is dangerous, but because two simultaneous changes
destroy attribution. When something breaks the next morning, you cannot tell
which change caused it, and you will spend more time on that question than the
tool would ever have saved.

One change at a time is normally stated at the level of code. It applies with
equal force at the level of the working day.

---

**Related:** [03 · Setup](07-installation-order.md) ·
[ADR-0004](../../decisions/0004-defer-a-third-party-skill-install.md)
