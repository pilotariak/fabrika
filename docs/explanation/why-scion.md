# Why Scion, why this delegation model

## From OpenRig to Scion

fabrika used to be two things: a plain-Markdown context pack (identity,
workflow, craft files), and an [OpenRig](https://github.com/mvschwarz/openrig)
rig definition (`rigs/pilotariak-dev/`) — a four-seat team (lead, builder,
code-reviewer, QA — OpenRig's own names, predating the rename to
`coordinator`/`developer`/`code-reviewer`/`qa-tester` below) with its own
`rig.yaml` pod/edge graph, `CULTURE.md`, and per-seat guidance files.

Moving to Scion kept the four roles and the values behind them (sign off
every commit, one concern per pull request, verify by effect not by
feeling — see the old `CULTURE.md`, folded into
[`pilotariak-context`](../../skills/pilotariak-context/SKILL.md) where it was
org-specific) but changed the mechanics underneath:

| OpenRig                                             | Scion                                                                       |
| --------------------------------------------------- | --------------------------------------------------------------------------- |
| `rig context add --git ...` once per machine        | Templates referenced directly by `gh://` URI, or synced to a Hub once       |
| `rig.yaml`'s pod/edge graph enforces delegation     | No DAG primitive — delegation is agent-initiated, following a skill's rules |
| `CLAUDE.local.md` managed blocks inject org content | A skill (`pilotariak-context`) mounted into the harness's skills directory  |
| `rig ps --nodes` cross-seat roster                  | `scion list`; "creating an agent subscribes you to its notifications"       |

The templates and skill live in this same repo, under `.scion/templates/`
and `skills/`, instead of a separate `rigs/` tree — Scion's template format
doesn't need a wrapper manifest the way OpenRig's bundle format did.

## Why only four roles (at the Small tier)

[`scion-frontiers/agent-team`](https://github.com/scion-frontiers/agent-team)
— the reference template library Scion's own docs point at — ships thirteen:
coordinator, architect, developer, web-builder, qa-tester, test-engineer,
code-reviewer, security-auditor, investigator, doc-writer, release-notes,
eng-manager, researcher. fabrika's Small-tier path uses four of those names
directly (`coordinator`, `developer`, `code-reviewer`, `qa-tester`), and
deliberately didn't fork the rest in preemptively — until
[the 14-role tree](#the-14-role-tree) below, which now carries most of the
remaining names, added for a different reason than "a recurring task shape
forced it" (see that section for the honest provenance). `web-builder` and
`release-notes` are the two upstream templates fabrika still doesn't have
anywhere — see ["Roles not added"](#roles-not-added) at the end of that
section for why.

The reason is sizing, not scope-cutting. `software-engineering-process`
(the ownership/delegation skill fabrika vendors and adapts from that same
repo — see "Vendored, not referenced" below) scales role count to the
*depth of the ownership tree* an issue needs:

- **Small** (depth 1): coordinator creates a developer and a reviewer.
  Everything in Pilotariak's actual history of changes has been this shape —
  one concern, one branch, one pull request.
- **Chunky/Large** (depth 3): coordinator creates a dedicated issue owner,
  which creates an investigator, architect, and engineering manager, which
  in turn creates developers and reviewers.

fabrika's `coordinator` template states explicitly that for a Small goal it
collapses straight to `coordinator`/`developer`/`code-reviewer`/`qa-tester`,
and that a goal needing more structure than that is a judgment call for the
person, not a cue to improvise a bigger tree under the Small path —
`coordinator`'s own Chunky/Large branch is the entry point for that shape
instead (there is no separate `pm` template anymore; see
["Template names"](#template-names) for why). Roles get added when
a recurring task shape actually forces it (see
[add a new role template](../how-to/add-a-role-template.md)), the same
discipline the skill itself names as "every orchestrating role must collapse
itself when handed less than its tier assumes."

Two roles used to be named here as open gaps rather than settled decisions:
`investigator` (bug reproduction and feature scoping before development
starts — `frontis` is a live deployed GraphQL gateway, so a production
incident needing root-causing isn't hypothetical, though upstream's own
investigator job is pre-dev scoping, not incident response specifically)
and `security-auditor` (Cloudflare Workers, an MCP server, and a GraphQL
gateway are real attack surface). Both are now real templates in
[the 14-role tree](#the-14-role-tree) below — added, like the rest of that
tree, because the person asked for that shape, not because a recurring
Pilotariak task forced it. Neither existed in the old OpenRig rig either, so
their earlier absence wasn't a regression — just unaddressed scope.

## The 14-role tree

A second tree exists in this pack: `po`, `db`, `eng-manager`, `investigator`,
`architect`, `swe-1`, `swe-2`, `test-engineer`, `ui-test`, `perf-test`,
`security-auditor`, `doc-writer`, `devops`, `sre` — fourteen templates,
not counting `coordinator` and `code-reviewer`, which this tree shares with
the Small tier above rather than forking its own copies of. It's the
Chunky/Large shape this document names above, but at depth 2:
`coordinator` creates `po`/`eng-manager` (and `investigator`/`architect`
directly, when the goal needs research or a design doc first) and
`eng-manager` creates its producers and verifiers directly, with no
dedicated-issue-owner layer between them (see
`software-engineering-process`'s own "Mapping onto fabrika's templates"
section for the full comparison). It mirrors a 14-agent SDLC-team shape
from outside this org. Its honest provenance: it was **not** added because
a recurring Pilotariak task shape forced it, which is the bar this document
otherwise insists on. It exists because the person asked for that specific
shape. Treat it accordingly:

- `coordinator` is the entry point for this tree, same template as the
  Small tier's — its own `agents.md` collapses straight back to a plain
  `developer` for anything Small, so asking for this tree doesn't obligate
  every goal to use all fourteen templates.
- The role names and splits come from a generic demo app, not from this
  org's repos. Some needed real adaptation: `po` targets GitHub issues (or
  Linear), not JIRA/Atlassian MCP, because nothing here uses JIRA;
  `devops` targets Cloudflare Workers and whatever `infrastructure`'s own
  IaC is, not Terraform/Cloud Run; `doc-writer` targets GraphQL-schema docs
  and this org's Diátaxis structure, not an OpenAPI/Swagger spec or a wiki
  that doesn't exist here; the reference shape's single CLI/codegen engineer
  split into `swe-1` (backend/Workers: `frontis`, `azkena`, `xilo`,
  `plaza`) and `swe-2` (UI/templates: `kancha`, `website`,
  `DESIGN.md`-driven tokens) against this org's actual repo boundary
  instead of its original CLI-vs-templates one — upstream has no template
  for either half of that split, and its own `web-builder` is a GCS
  static-site/dashboard-publishing role, a bad fit for `swe-2`'s actual job
  (`kancha`'s Expo app, `website`'s Astro site), so it wasn't used for
  either.
  `security-auditor` closes the gap named just above, adapted from the
  upstream template of the same name — but there's no dedicated
  security-auditor *skill* in `scion-frontiers/agent-team` to lean on
  either, so it works from each repo's own existing scanning instead of a
  vendored one. `investigator` and `architect` are now real templates too,
  ported from upstream and adapted: `investigator` does bug
  reproduction/feature scoping (upstream's actual job for the role, not
  production-incident response), and `architect` writes a design doc for a
  goal that needs one, reading the investigator's findings first rather
  than re-deriving them. Both are created directly by `coordinator`, never
  by a dedicated sub-coordinator — see the mapping in
  `software-engineering-process`.
- None of this tree has run end to end yet, any more than the four-role path
  has (see "Unresolved, on purpose" below). Its first real test will show
  whether the producer/verifier split and the harness choices per role
  actually hold up, the same way the four-role path's first test is still
  pending.
- "Delegation is agent-initiated" (below) argues free-text handoffs are
  enough *because* this setup runs one issue in flight at a time.
  `eng-manager` fanning out up to five verifiers concurrently against one
  branch is exactly the concurrency that argument names as the trigger for
  formalizing handoffs into a schema instead. This tree doesn't do that
  yet — treat the five-verifier fan-out as unproven at that trigger, not as
  evidence the freeform-message approach scales.

### Roles not added

`web-builder` and `release-notes` are upstream's remaining two templates,
and neither has a fabrika equivalent, for different reasons:

- `web-builder` publishes static sites/dashboards to a GCS bucket and
  updates a `chronicler` agent on milestones — a domain (static hosting,
  GCS, a chronicler concept) nothing in this org's repo map has. It isn't a
  renamed `swe-2`; it's a job fabrika doesn't do.
- `release-notes` (daily semantic release notes generated from git history)
  was considered and explicitly declined, not overlooked — ask the person
  who requested the rest of this tree before reviving it rather than
  treating its absence as an oversight.

`researcher` — general-purpose, sourced research reports — was added, but
it isn't part of either tree: any role can start it ad hoc for a question
that needs a structured answer before the tree's own work continues. See
[the agent templates reference](../reference/agents.md#standalone).

## Delegation is agent-initiated

There's no engine enforcing who may start whom. `coordinator`'s `agents.md`
is explicit that **the coordinator**, not the developer, creates the
reviewer and QA — because creating an agent is what subscribes the creator
to its notifications. A developer that spawned its own reviewer would leave
the coordinator with no way to know what the reviewer found. The handoff is
a `scion message` from developer to coordinator carrying a branch name and
commit SHA; the coordinator starts review and QA against that exact branch,
landing them in a worktree that shares `.git` with the developer's — which
is also why nothing needs to be pushed for the reviewer to see the change.

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

## Vendored, not referenced

`software-engineering-process`, `code-review`, `agent-state-continuity`,
`docs-update`, and `artifact-durability` all originate in
[`scion-frontiers/agent-team`](https://github.com/scion-frontiers/agent-team)
(Apache-2.0), but fabrika does not reference them there by `gh://` URI —
each one is copied into `skills/` in this repo and edited to match fabrika's
actual rules (see
[the agent templates reference](../reference/agents.md) for which template
uses which). This was a deliberate choice over referencing them unpinned:

- **Pro:** an upstream change to `scion-frontiers/agent-team` cannot change
  fabrika's agent behavior without a PR here. No surprise drift.
- **Con:** fabrika does not get upstream fixes or improvements for free
  either — someone has to notice them and re-sync.
- Three of the five needed real changes, not a straight copy, because the
  upstream text assumed things that aren't true here: `artifact-durability`
  assumed pushing to a remote is how you prove work is safe, which
  contradicts "the person decides what gets pushed" (`coordinator`'s own
  rule, and "Delegation is agent-initiated" below); `code-review` assumed a
  `CLAUDE.md` and an already-open PR to diff against, where fabrika reviews
  happen pre-push in a shared worktree; `software-engineering-process`'s
  generic coordinator/architect/EM vocabulary needed a mapping onto what
  fabrika's two trees actually have — see that skill's own "Mapping onto
  fabrika's templates" section.
- Each vendored `SKILL.md` carries a one-line attribution naming the
  upstream commit it was copied from
  (`scion-frontiers/agent-team@3650f1b`), so a later re-sync has something
  to diff against.

## Template names

fabrika's templates were originally named independently of
`scion-frontiers/agent-team` (`lead`, `pm`, `builder`, `qa`, `tpm`, `doc`,
`secops`, `swe-test`, `reviewer`). They were renamed to match upstream's
own names wherever a fabrika template maps cleanly onto one upstream role,
on the view that matching names make the mapping legible without a lookup
table — `docs/reference/agents.md` still carries one anyway, since not
every name lines up by itself. Three places didn't rename cleanly, because
more than one fabrika template mapped to the same upstream name:

- **`lead` and `pm` → `coordinator`.** Both were already two copies of the
  same role: `pm`'s own Small-tier branch said "create a builder exactly
  the way `lead`'s `agents.md` does." They're merged into one `coordinator`
  template that sizes the goal itself and either collapses to Small or
  delegates to `po`/`eng-manager` — the same sizing-and-collapse logic
  upstream's own `coordinator` does.
- **`code-reviewer` and `reviewer` → `code-reviewer`.** `reviewer`'s own
  `agents.md` said outright it was "fabrika's `code-reviewer` role under
  the name the reference 14-agent shape uses" — same skills file, same
  checks, one extra paragraph (now backported into `code-reviewer`) about
  the approval being a gate, not a formality. One template, started by
  either `coordinator` or `eng-manager`.
- **`builder`/`swe-1`/`swe-2` → `developer`, `swe-1`, `swe-2`.** All three
  are upstream's `developer` role in spirit, but `swe-1` (backend/Workers)
  and `swe-2` (UI/templates) split a job upstream doesn't split this way,
  and upstream's closest named alternative for a UI-flavored developer
  (`web-builder`) is the wrong domain (see ["Roles not
  added"](#roles-not-added) above). Only `builder` — the Small-tier,
  single-developer role — renamed to `developer`; `swe-1`/`swe-2` kept
  their fabrika-specific names rather than force a bad-fit upstream one.

## Unresolved, on purpose

- Nothing here has run end to end yet. See the repo's `CONTRIBUTING.md` and
  [the getting-started tutorial](../tutorials/getting-started.md) for the
  first real test.
