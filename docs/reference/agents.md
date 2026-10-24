# Agent templates

Each `scion-agent.yaml` is the authoritative field list for its template —
`agent_instructions`, `system_prompt`, `default_harness_config`, `skills`.
For what every field can mean, see Scion's own
[Agent Configuration reference](https://googlecloudplatform.github.io/scion/reference/agent-config/),
not a copy of it here.

Template names follow `scion-frontiers/agent-team`'s own naming where a
template maps cleanly onto one of its roles. Where fabrika splits or merges
a role for its own reasons, see
[why these names diverge from upstream](../explanation/why-scion.md#template-names)
for which are which.

## Small tier

`coordinator` creates a `developer` directly; `developer` hands off to
`coordinator`, which creates `code-reviewer` and `qa-tester`. See
[why this setup stops at four roles at the Small tier](../explanation/why-scion.md#why-only-four-roles-at-the-small-tier).

| Template        | Source                                                                     |
| --------------- | -------------------------------------------------------------------------- |
| `coordinator`   | [`.scion/templates/coordinator/`](../../.scion/templates/coordinator/)     |
| `developer`     | [`.scion/templates/developer/`](../../.scion/templates/developer/)         |
| `code-reviewer` | [`.scion/templates/code-reviewer/`](../../.scion/templates/code-reviewer/) |
| `qa-tester`     | [`.scion/templates/qa-tester/`](../../.scion/templates/qa-tester/)         |

## Chunky/Large tree

`coordinator` creates `po` and `eng-manager` (and, for a goal that needs
research or a design doc first, `investigator` and/or `architect`);
`eng-manager` creates the producers (`db`, `swe-1`, `swe-2`) and, on
handoff, the verifiers (`test-engineer`, `ui-test`, `perf-test`,
`security-auditor`, `code-reviewer`); `coordinator` creates the ship-phase
roles (`doc-writer`, `devops`, `sre`) once eng-manager reports green. See
[its own section](../explanation/why-scion.md#the-14-role-tree) for what
this tree is and isn't — including that it's untested and adapted from a
generic SDLC shape, not forced by a recurring Pilotariak task.

`coordinator` and `code-reviewer` are shared with the Small tier above —
there's no separate Chunky/Large-only coordinator or reviewer template.

| Template           | Source                                                                           |
| ------------------ | -------------------------------------------------------------------------------- |
| `po`               | [`.scion/templates/po/`](../../.scion/templates/po/)                             |
| `db`               | [`.scion/templates/db/`](../../.scion/templates/db/)                             |
| `eng-manager`      | [`.scion/templates/eng-manager/`](../../.scion/templates/eng-manager/)           |
| `investigator`     | [`.scion/templates/investigator/`](../../.scion/templates/investigator/)         |
| `architect`        | [`.scion/templates/architect/`](../../.scion/templates/architect/)               |
| `swe-1`            | [`.scion/templates/swe-1/`](../../.scion/templates/swe-1/)                       |
| `swe-2`            | [`.scion/templates/swe-2/`](../../.scion/templates/swe-2/)                       |
| `test-engineer`    | [`.scion/templates/test-engineer/`](../../.scion/templates/test-engineer/)       |
| `ui-test`          | [`.scion/templates/ui-test/`](../../.scion/templates/ui-test/)                   |
| `perf-test`        | [`.scion/templates/perf-test/`](../../.scion/templates/perf-test/)               |
| `security-auditor` | [`.scion/templates/security-auditor/`](../../.scion/templates/security-auditor/) |
| `doc-writer`       | [`.scion/templates/doc-writer/`](../../.scion/templates/doc-writer/)             |
| `devops`           | [`.scion/templates/devops/`](../../.scion/templates/devops/)                     |
| `sre`              | [`.scion/templates/sre/`](../../.scion/templates/sre/)                           |

## Standalone

`researcher` isn't part of either tree above — any role can start it
ad hoc for a research question that needs a structured, sourced report
before the tree's own work continues. It runs on `gemini-cli`, not this
pack's usual `codex`/`claude` split — see its own `agents.md` for why.

| Template     | Source                                                               |
| ------------ | -------------------------------------------------------------------- |
| `researcher` | [`.scion/templates/researcher/`](../../.scion/templates/researcher/) |

## Skills vendored from scion-frontiers/agent-team

fabrika doesn't reference these by `gh://` URI at the source — each is
copied into `skills/` here and adapted to fabrika's own rules. See
["Vendored, not referenced"](../explanation/why-scion.md#vendored-not-referenced)
for why, and each skill's own attribution line for the upstream commit it
was copied from.

| Skill                          | Source                                                                               | Used by                                                                                                |
| ------------------------------ | ------------------------------------------------------------------------------------ | ------------------------------------------------------------------------------------------------------ |
| `software-engineering-process` | [`skills/software-engineering-process/`](../../skills/software-engineering-process/) | `coordinator`, `code-reviewer`, `eng-manager`, `investigator`, `architect`                             |
| `code-review`                  | [`skills/code-review/`](../../skills/code-review/)                                   | `code-reviewer`                                                                                        |
| `agent-state-continuity`       | [`skills/agent-state-continuity/`](../../skills/agent-state-continuity/)             | `coordinator`                                                                                          |
| `docs-update`                  | [`skills/docs-update/`](../../skills/docs-update/)                                   | `doc-writer`                                                                                           |
| `artifact-durability`          | [`skills/artifact-durability/`](../../skills/artifact-durability/)                   | `coordinator`, `eng-manager`, `ui-test`, `perf-test`, `sre`, `investigator`, `architect`, `researcher` |
