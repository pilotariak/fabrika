# Why Scion, why this delegation model

## From OpenRig to Scion

fabrika used to be two things: a plain-Markdown context pack (identity,
workflow, craft files), and an [OpenRig](https://github.com/mvschwarz/openrig)
rig definition (`rigs/pilotariak-dev/`) — a four-seat team (lead, builder,
code-reviewer, QA) with its own `rig.yaml` pod/edge graph, `CULTURE.md`, and
per-seat guidance files.

Moving to Scion kept the four roles and the values behind them (sign off
every commit, one concern per pull request, verify by effect not by
feeling — see the old `CULTURE.md`, folded into
[`pilotariak-context`](../../skills/pilotariak-context/SKILL.md) where it was
org-specific) but changed the mechanics underneath:

| OpenRig | Scion |
|---|---|
| `rig context add --git ...` once per machine | Templates referenced directly by `gh://` URI, or synced to a Hub once |
| `rig.yaml`'s pod/edge graph enforces delegation | No DAG primitive — delegation is agent-initiated, following a skill's rules |
| `CLAUDE.local.md` managed blocks inject org content | A skill (`pilotariak-context`) mounted into the harness's skills directory |
| `rig ps --nodes` cross-seat roster | `scion list`; "creating an agent subscribes you to its notifications" |

The templates and skill live in this same repo, under `.scion/templates/`
and `skills/`, instead of a separate `rigs/` tree — Scion's template format
doesn't need a wrapper manifest the way OpenRig's bundle format did.

## Why only four roles

[`scion-frontiers/agent-team`](https://github.com/scion-frontiers/agent-team)
— the reference template library Scion's own docs point at — ships thirteen:
coordinator, architect, developer, web-builder, qa-tester, test-engineer,
code-reviewer, security-auditor, investigator, doc-writer, release-notes,
eng-manager, researcher. fabrika uses four, and deliberately doesn't fork the
rest in preemptively.

The reason is sizing, not scope-cutting. `software-engineering-process`
(the ownership/delegation skill fabrika borrows from that same repo) scales
role count to the *depth of the ownership tree* an issue needs:

- **Small** (depth 1): coordinator creates a developer and a reviewer.
  Everything in Pilotariak's actual history of changes has been this shape —
  one concern, one branch, one pull request.
- **Chunky/Large** (depth 3): coordinator creates a dedicated issue owner,
  which creates an investigator, architect, and engineering manager, which
  in turn creates developers and reviewers.

fabrika's `lead` template states explicitly that every issue here runs at
Small tier, and that a goal needing more structure is a judgment call for
the person, not a cue to manufacture the missing roles. Roles get added when
a recurring task shape actually forces it (see
[add a new role template](../how-to/add-a-role-template.md)), the same
discipline the skill itself names as "every orchestrating role must collapse
itself when handed less than its tier assumes."

Two roles came close enough to be worth naming as open gaps rather than
settled decisions: `investigator` (production-incident debugging — `frontis`
is a live deployed GraphQL gateway, so this isn't hypothetical) and
`security-auditor` (Cloudflare Workers, an MCP server, and a GraphQL gateway
are real attack surface). Neither existed in the old OpenRig rig either, so
their absence here isn't a regression — just unaddressed scope, worth adding
if the corresponding task shape actually recurs.

## Delegation is agent-initiated

There's no engine enforcing who may start whom. `lead`'s `agents.md` is
explicit that **the lead**, not the builder, creates the reviewer and QA —
because creating an agent is what subscribes the creator to its
notifications. A builder that spawned its own reviewer would leave the lead
with no way to know what the reviewer found. The handoff is a
`scion message` from builder to lead carrying a branch name and commit SHA;
the lead starts review and QA against that exact branch, landing them in a
worktree that shares `.git` with the builder's — which is also why nothing
needs to be pushed for the reviewer to see the change.

This is a deliberate, minimal answer to a question a much larger Scion
project answers differently.
[OASE](https://github.com/GoogleCloudPlatform/LifeSciences/tree/main/applications/open-agentic-science-engine) —
a pre-clinical drug-discovery system built on Scion, with 22 role templates —
splits orchestration into two concerns with different cadences
("scientific" and "operational"), and formalizes every handoff as a
versioned **work-order** YAML artifact with a schema, required fields, and
lifecycle states, rather than a freeform message. That's the right shape at
OASE's scale: many concurrent specialists, machine-checked deliverables,
audit requirements.

fabrika doesn't need that yet. Four roles, one issue in flight at a time,
and a message carrying a branch and a SHA is enough structure for what this
org actually does. If that stops being true — concurrent issues, a handoff
that keeps arriving malformed, a need to audit exactly what was asked for
versus what was delivered — formalizing the brief as a small schema (instead
of free text in a `scion message`) is the next deliberate step, not
something to pre-build now.

## Unresolved, on purpose

- The two external skills this setup depends on
  (`software-engineering-process`, `code-review`) are referenced unpinned —
  an upstream change there changes fabrika's agent behavior without a PR
  here. Acceptable for now because both are small, stable, and reviewed
  before being adopted; pin them to a commit if that stops being true.
- Nothing here has run end to end yet. See the repo's `CONTRIBUTING.md` and
  [the getting-started tutorial](../tutorials/getting-started.md) for the
  first real test.
