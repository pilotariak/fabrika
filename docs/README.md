# fabrika documentation

fabrika is the [Scion](https://github.com/GoogleCloudPlatform/scion) setup
for the Pilotariak organization: agent templates — the Small-tier
`coordinator`/`developer`/`code-reviewer`/`qa-tester` path, plus a 14-role
Chunky/Large tree also starting at `coordinator` — the `pilotariak-context`
skill that carries org facts no single repo's own `AGENTS.md` states, and a
handful of skills vendored and adapted from `scion-frontiers/agent-team`.
This documentation is organized
following the [Diátaxis framework](https://diataxis.fr/), which structures
content by what you're trying to do, not by what exists in the repo.

## Where to start

| I want to...                                        | Go to                       |
| --------------------------------------------------- | --------------------------- |
| Run my first agent against a Pilotariak repo        | [Tutorials](tutorials/)     |
| Add a new role template                             | [How-to Guides](how-to/)    |
| Look up a template's or skill's exact fields        | [Reference](reference/)     |
| Understand why Scion, and why this delegation model | [Explanation](explanation/) |

## Tutorials

- [Run your first agent with fabrika](tutorials/getting-started.md)

## How-to Guides

- [Add a new role template](how-to/add-a-role-template.md)
- [Sync templates to a Hub instead of resolving by `gh://`](how-to/sync-templates-to-hub.md)

## Reference

- [Reference index](reference/README.md) — points at `skills/` and
  `.scion/templates/`, the actual source, instead of duplicating it

## Explanation

- [Why Scion, why this delegation model](explanation/why-scion.md)

---

*Documentation structured with [Diátaxis](https://diataxis.fr/)*
