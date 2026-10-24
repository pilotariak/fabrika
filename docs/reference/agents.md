# Agent templates

Each `scion-agent.yaml` is the authoritative field list for its template —
`agent_instructions`, `system_prompt`, `default_harness_config`, `skills`.
For what every field can mean, see Scion's own
[Agent Configuration reference](https://googlecloudplatform.github.io/scion/reference/agent-config/),
not a copy of it here.

## Small tier

`lead` creates a `builder` directly; `builder` hands off to `lead`, which
creates `code-reviewer` and `qa`. See
[why this setup stops at four roles at the Small tier](../explanation/why-scion.md#why-only-four-roles-at-the-small-tier).

| Template        | Source                                                                     |
| --------------- | -------------------------------------------------------------------------- |
| `lead`          | [`.scion/templates/lead/`](../../.scion/templates/lead/)                   |
| `builder`       | [`.scion/templates/builder/`](../../.scion/templates/builder/)             |
| `code-reviewer` | [`.scion/templates/code-reviewer/`](../../.scion/templates/code-reviewer/) |
| `qa`            | [`.scion/templates/qa/`](../../.scion/templates/qa/)                       |

## 14-role tree

`pm` creates `po` and `tpm`; `tpm` creates the producers (`db`, `swe-1`,
`swe-2`) and, on handoff, the verifiers (`swe-test`, `ui-test`, `perf-test`,
`secops`, `reviewer`); `pm` creates the ship-phase roles (`doc`, `devops`,
`sre`) once tpm reports green. See
[its own section](../explanation/why-scion.md#the-14-role-tree) for what
this tree is and isn't — including that it's untested and adapted from a
generic SDLC shape, not forced by a recurring Pilotariak task.

| Template    | Source                                                             |
| ----------- | ------------------------------------------------------------------ |
| `pm`        | [`.scion/templates/pm/`](../../.scion/templates/pm/)               |
| `po`        | [`.scion/templates/po/`](../../.scion/templates/po/)               |
| `db`        | [`.scion/templates/db/`](../../.scion/templates/db/)               |
| `tpm`       | [`.scion/templates/tpm/`](../../.scion/templates/tpm/)             |
| `swe-1`     | [`.scion/templates/swe-1/`](../../.scion/templates/swe-1/)         |
| `swe-2`     | [`.scion/templates/swe-2/`](../../.scion/templates/swe-2/)         |
| `swe-test`  | [`.scion/templates/swe-test/`](../../.scion/templates/swe-test/)   |
| `ui-test`   | [`.scion/templates/ui-test/`](../../.scion/templates/ui-test/)     |
| `perf-test` | [`.scion/templates/perf-test/`](../../.scion/templates/perf-test/) |
| `secops`    | [`.scion/templates/secops/`](../../.scion/templates/secops/)       |
| `reviewer`  | [`.scion/templates/reviewer/`](../../.scion/templates/reviewer/)   |
| `doc`       | [`.scion/templates/doc/`](../../.scion/templates/doc/)             |
| `devops`    | [`.scion/templates/devops/`](../../.scion/templates/devops/)       |
| `sre`       | [`.scion/templates/sre/`](../../.scion/templates/sre/)             |

## Skills vendored from scion-frontiers/agent-team

fabrika doesn't reference these by `gh://` URI at the source — each is
copied into `skills/` here and adapted to fabrika's own rules. See
["Vendored, not referenced"](../explanation/why-scion.md#vendored-not-referenced)
for why, and each skill's own attribution line for the upstream commit it
was copied from.

| Skill                          | Source                                                                               | Used by                                          |
| ------------------------------ | ------------------------------------------------------------------------------------ | ------------------------------------------------ |
| `software-engineering-process` | [`skills/software-engineering-process/`](../../skills/software-engineering-process/) | `lead`, `code-reviewer`, `pm`, `tpm`, `reviewer` |
| `code-review`                  | [`skills/code-review/`](../../skills/code-review/)                                   | `code-reviewer`, `reviewer`                      |
| `agent-state-continuity`       | [`skills/agent-state-continuity/`](../../skills/agent-state-continuity/)             | `pm`                                             |
| `docs-update`                  | [`skills/docs-update/`](../../skills/docs-update/)                                   | `doc`                                            |
| `artifact-durability`          | [`skills/artifact-durability/`](../../skills/artifact-durability/)                   | `pm`, `tpm`, `ui-test`, `perf-test`, `sre`       |
