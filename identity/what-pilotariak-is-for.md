# What Pilotariak is for

Pilotariak is a platform for Basque pelota competition management: leagues, clubs,
categories, specialties, results. It is split across repositories by concern, not
by team — most of it is maintained by one person plus agents, so the split exists
to keep each repository's blast radius small, not to coordinate humans.

## Repo map

| Repo             | Purpose                                                                                                                                             | Stack                                          |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------- |
| `frontis`        | GraphQL gateway over the competition data (clubs, competitions, categories, results, specialties) — the source of truth every other repo reads from | Bun, GraphQL Hive, Cloudflare Workers, D1      |
| `kancha`         | Mobile app for competition management                                                                                                               | React Native, Expo, TypeScript                 |
| `azkena`         | MCP server exposing competition data as tools for AI assistants (Claude Desktop, Cursor, Copilot Workspace)                                         | Cloudflare Worker, `@modelcontextprotocol/sdk` |
| `xilo`           | Slack bot: slash commands plus a natural-language agent over Frontis                                                                                | Cloudflare Worker, Workers AI                  |
| `plaza`          | AT Protocol (Bluesky) surface — lexicons and workers                                                                                                | Cloudflare Workers                             |
| `website`        | Public website                                                                                                                                      | Astro                                          |
| `decisions`      | Architecture Decision Records for the org                                                                                                           | Markdown                                       |
| `infrastructure` | Shared infrastructure-as-code                                                                                                                       | —                                              |
| `.github`        | Org profile, org-wide workflows, CODEOWNERS defaults                                                                                                | —                                              |

`frontis` is the hub: `kancha`, `azkena`, `xilo` and `plaza` all read competition
data through its GraphQL gateway. A schema change in `frontis` is a change with
downstream readers — check who queries the field you are touching before you
change its shape.

## How a change is judged

Every repo in the org (this one included) shares the same bar, regardless of
stack:

- **Signed off.** Commits carry a DCO `Signed-off-by` trailer
  ([CONTRIBUTING.md](../CONTRIBUTING.md)). A PR with an unsigned commit does not
  merge.
- **Conventional commit.** Subject lines are `type(scope): subject`
  (`feat`, `fix`, `chore`, `docs`, `change`, `deps`, …). `release-please` reads
  this history to cut releases and write changelogs — a wrong type produces a
  wrong changelog entry or no release at all.
- **Hooks pass.** `pre-commit` runs `hawkeye` (SPDX license headers), formatting
  (`dprint` or the repo's own formatter), `gitleaks`, and schema checks for
  Renovate and GitHub Actions config. These run locally before they run in CI.
- **The repo's own checks pass.** `make help` lists them; most repos expose
  `test`, `lint`, and either `typecheck`/`types`. See
  [proving a change](../craft/proving-a-change.md) for the per-repo commands.
- **Design tokens stay generated, not hand-edited**, in repos that have a
  `DESIGN.md` (`frontis`, `kancha`, `website`): the token source is `DESIGN.md`,
  and `make tokens-check` fails the build on drift.

There is no cross-repo CI gate — each repository is released independently via
its own `release-please.yml`. Coordination across repos (e.g. a `frontis` schema
change that `kancha` depends on) is a judgment call you make by reading the
downstream repo, not something a pipeline enforces for you.
