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

## Documentation

Start at [docs/](docs/) — organized with [Diátaxis](https://diataxis.fr/): a
[tutorial](docs/tutorials/getting-started.md) to run your first agent, a
[how-to](docs/how-to/) for adding a role template or syncing templates to a
Hub, [reference](docs/reference/) for exact template and skill fields, and
[explanation](docs/explanation/why-scion.md) for why this setup looks the
way it does — including its current status and known gaps.

## Contributing

Pull requests are welcome, especially when you find something here that's
stale or wrong — this org's repos move faster than this pack will. Keep pages
short, point at the source repo instead of copying its content, and describe
commands only after you've run them. See [CONTRIBUTING.md](CONTRIBUTING.md).

## License

See [LICENSE](LICENSE).
