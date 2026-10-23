# Reference

This section is intentionally thin. The actual reference material — exact
fields, exact file contents, exact skill URIs — already lives where Scion
reads it from, and duplicating it here would just give it a second copy to
go stale.

## Templates

| Template                                                | Source                                                    |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| `lead`                                                    | [`.scion/templates/lead/`](../../.scion/templates/lead/) |
| `builder`                                                 | [`.scion/templates/builder/`](../../.scion/templates/builder/) |
| `code-reviewer`                                           | [`.scion/templates/code-reviewer/`](../../.scion/templates/code-reviewer/) |
| `qa`                                                      | [`.scion/templates/qa/`](../../.scion/templates/qa/) |

Each `scion-agent.yaml` is the authoritative field list for that template —
`agent_instructions`, `system_prompt`, `default_harness_config`, `skills`.
For what every field in that file can mean, see Scion's own
[Agent Configuration reference](https://googlecloudplatform.github.io/scion/reference/agent-config/),
not a copy of it here.

## Skill

| Skill                 | Source                                                      |
| ------------------------ | -------------------------------------------------------------- |
| `pilotariak-context`  | [`skills/pilotariak-context/`](../../skills/pilotariak-context/) |

Its `SKILL.md` is the index; `references/` underneath it holds the per-repo
command table, the wrong-claim tells, and the frontis agentic-automation
notes.

## External skills this setup depends on

Not fabrika's to document — read them at the source:

- [`gh://scion-frontiers/agent-team/software-engineering-process`](https://github.com/scion-frontiers/agent-team/tree/main/skills/software-engineering-process)
- [`gh://scion-frontiers/agent-team/code-review`](https://github.com/scion-frontiers/agent-team/tree/main/skills/code-review)

Both are referenced unpinned — see
[why this setup looks the way it does](../explanation/why-scion.md) for the
tradeoff.
