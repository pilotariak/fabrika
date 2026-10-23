# fabrika

[![License: Apache-2.0](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://github.com/Pilotariak/fabrika/blob/main/LICENSE)
[![OpenSSF Scorecard](https://api.scorecard.dev/projects/github.com/Pilotariak/fabrika/badge)](https://scorecard.dev/viewer/?uri=github.com/Pilotariak/fabrika)

fabrika is the [Scion](https://github.com/GoogleCloudPlatform/scion) setup
for the **Pilotariak** organization — `frontis`, `kancha`, `azkena`, `xilo`,
`plaza`, `website`, `decisions`, `infrastructure`. It holds the agent
templates and the shared org-context skill, so an agent working on any of
those repos doesn't relearn "what is this org, how do changes get made here"
every session. Short on purpose, points at each repo's own `AGENTS.md`/CI
instead of copying them, and expected to drift — disagreements with a repo's
actual source are the repo's to win.

## Who it's for

Anyone running Scion agents against more than one Pilotariak repo.

## What's inside

| Path                              | Covers                                                                                                           |
| --------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| `skills/pilotariak-context/`      | The org-wide skill: repo map, DCO sign-off rule, per-repo prove-it commands, generated-file source-of-truth rule |
| `.scion/templates/lead/`          | Scopes a goal, delegates to the builder, keeps the PR description true                                           |
| `.scion/templates/builder/`       | Makes the change, proves it, hands off to review and QA                                                          |
| `.scion/templates/code-reviewer/` | Independent second pass against the repo's own maps and the actual code                                          |
| `.scion/templates/qa/`            | Runs the checks the change calls for, reports what each one proved                                               |

## Use it

No per-repo template install — any Scion agent can run straight off this
repo by `gh://` URI. The machine still needs the normal Scion prerequisites
first: `scion server start` once, then `scion init` inside the target repo,
harness auth for whichever harness the template defaults to (`codex` for
lead/code-reviewer, `claude` for builder/qa), and a git identity available
inside the container — the templates stop and ask rather than guessing one.

```bash
cd kancha   # or frontis, azkena, xilo, plaza, website
scion start lead "<your goal>" \
  --type gh://Pilotariak/fabrika/.scion/templates/lead
```

The target repo is linked to your local Hub on first use and each agent gets
its own git worktree by default — worktrees of the same repo share `.git`,
which is what lets the reviewer and QA see the builder's branch without
anything being pushed. The lead creates the builder, and the builder reports
back to the lead rather than spawning review/QA itself — see each template's
own `agents.md` for the exact handoff and why.

If `gh://` latency on every start becomes a problem, sync the templates to
your Hub once instead, and reference them by bare name:

```bash
cd fabrika && scion templates sync --all
# elsewhere: scion start lead "<your goal>" --type lead
```

A synced copy needs a re-sync (`scion templates sync --all`) after every
edit here; resolving a template straight by `gh://` URI is expected to pick
up edits on the next start, but that path is also the least exercised part
of this setup — see Status below.

## Status

Untested end to end — the `gh://Pilotariak/fabrika/...` URIs these templates
and this README use only resolve once this lands on `main`, and running them
for the first time is the actual test. The two external skills
(`gh://scion-frontiers/agent-team/software-engineering-process` and
`.../code-review`) are referenced unpinned, so they can change out from under
these templates; pin them to a commit or tag if that becomes a problem.

## Contributing

Pull requests are welcome, especially when you find something here that's
stale or wrong — this org's repos move faster than this pack will. Keep pages
short, point at the source repo instead of copying its content, and describe
commands only after you've run them. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

See [LICENSE](LICENSE).
