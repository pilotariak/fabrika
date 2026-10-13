# fabrika

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/Pilotariak/fabrika/blob/main/LICENSE)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/Pilotariak/fabrika/badge)](https://scorecard.dev/viewer/?uri=github.com/Pilotariak/fabrika)

A context pack that helps a coding agent work across the **Pilotariak**
organization — `frontis`, `kancha`, `azkena`, `xilo`, `plaza`, `website`,
`decisions`, `infrastructure` — instead of relearning each repository's
conventions from scratch every session. Short on purpose, points at each
repo's own `AGENTS.md`/CI instead of copying them, and expected to drift —
disagreements with a repo's actual source are the repo's to win.

## Who it's for

- Anyone (human or agent) touching more than one Pilotariak repo, where the
  friction is re-explaining "what is this org, how do changes get made here"
  every time.
- Works standalone: every file here is plain Markdown, readable straight from
  this repository by any agent or any human. No installer required.

## What's inside

| File                                   | Covers                                                                  |
| -------------------------------------- | ----------------------------------------------------------------------- |
| `identity/what-pilotariak-is-for.md`   | What Pilotariak is, the repo map, how a change is judged org-wide       |
| `workflow/contributing-with-agents.md` | The loop: sign-off, conventional commits, hooks, review, release-please |
| `workflow/agentic-automation.md`       | The `gh aw`-based agent CI piloted in `frontis`, and how to extend it   |
| `craft/proving-a-change.md`            | Per-repo commands that actually prove a change works                    |
| `craft/tells-of-a-wrong-claim.md`      | Moments you're about to overstate something, and the cheapest check     |
| `manifest.yaml`                        | The pack manifest                                                       |

## Use it directly

Point an agent at this repo, or hand it a specific file:

```text
Read https://github.com/Pilotariak/fabrika before working on <repo>.
```

## Use it with OpenRig

If the agents working across these repos run as an
[OpenRig](https://github.com/mvschwarz/openrig) rig, install this as a context
pack once per machine:

```bash
rig context add --git https://github.com/Pilotariak/fabrika.git
rig context list
```

Load it:

| When                                     | Command                                                            |
| ---------------------------------------- | ------------------------------------------------------------------ |
| Starting work on a Pilotariak repo       | `rig context profile fabrika --situation fresh`                    |
| Reviewing a pull request                 | `rig context profile fabrika --situation fresh --profile reviewer` |
| After a compaction, `/clear`, or restart | `rig context profile fabrika --situation post-compaction`          |
| One file                                 | `rig context get fabrika/craft/proving-a-change.md`                |
| Everything                               | `rig context get fabrika`                                          |

Check for and take updates:

```bash
rig context source inspect fabrika
rig context source update fabrika
```

## Rig catalog

[`pilotariak-dev`](rigs/pilotariak-dev/README.md) is a four-seat OpenRig team
(lead, builder, code-reviewer, QA) for Pilotariak repos. It loads this pack at
seat startup and is deliberately lean — no borrowed skill tree, see its own
README for the tradeoff.

## Contributing

Pull requests are welcome, especially when you find something here that's
stale or wrong — this org's repos move faster than this pack will. Keep pages
short, point at the source repo instead of copying its content, and describe
commands only after you've run them. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

See [LICENSE](LICENSE).
