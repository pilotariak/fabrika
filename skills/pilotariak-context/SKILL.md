---
name: pilotariak-context
description: >-
  Org facts for the Pilotariak repos (frontis, kancha, azkena, xilo, plaza,
  website, decisions, infrastructure) that no single repo's own AGENTS.md
  covers: the repo map, why frontis is the hub, the DCO sign-off rule, and
  where generated files come from. Use at the start of any task that touches
  one of these repos, before reading that repo's own AGENTS.md.
---

# Pilotariak context

Pilotariak is a platform for Basque pelota competition management: leagues,
clubs, categories, specialties, results. It is split across repositories by
concern, not by team — one person plus agents maintain it, so the split keeps
each repo's blast radius small, not to coordinate humans.

This skill is a map, not the territory. Where it disagrees with a repo's own
`AGENTS.md` or current CI, the repo is right — fix this skill in a separate
pull request to `Pilotariak/fabrika` instead of trusting the stale copy.

## Repo map

| Repo             | Purpose                                                                             | Stack                                     |
| ---------------- | ------------------------------------------------------------------------------------ | ------------------------------------------ |
| `frontis`        | GraphQL gateway over competition data — the source of truth every other repo reads   | Bun, GraphQL Hive, Cloudflare Workers, D1 |
| `kancha`         | Mobile app for competition management                                                | React Native, Expo, TypeScript            |
| `azkena`         | MCP server exposing competition data as tools for AI assistants                      | Cloudflare Worker, `@modelcontextprotocol/sdk` |
| `xilo`           | Slack bot: slash commands plus a natural-language agent over Frontis                 | Cloudflare Worker, Workers AI (uses `npm`, not `bun`) |
| `plaza`          | AT Protocol (Bluesky) surface — lexicons and workers                                 | Cloudflare Workers                        |
| `website`        | Public website                                                                       | Astro                                      |
| `decisions`      | Architecture Decision Records for the org                                            | Markdown                                   |
| `infrastructure` | Shared infrastructure-as-code                                                        | —                                           |

## frontis is the hub

`kancha`, `azkena`, `xilo`, and `plaza` all read competition data through
`frontis`'s GraphQL gateway. Before changing a field's shape in `frontis`,
check whether a downstream repo queries it — a passing `frontis` test suite
proves nothing about what it breaks downstream. There is no cross-repo CI
gate for this; it's a judgment call you make by reading the downstream repo.

## Sign off every commit

Every commit carries a DCO `Signed-off-by` trailer (`git commit -s`) — not
optional, per each repo's own `CONTRIBUTING.md`. Subject lines are
conventional-commit (`type(scope): subject`); `release-please` reads this
history to cut releases, so a wrong type produces a wrong changelog entry or
no release at all. **Never add a `Co-authored-by` trailer** — sign-off is the
attribution mechanism here, not co-authorship. A PR with an unsigned commit
does not merge regardless of code quality.

If `git config user.name`/`user.email` is empty inside the agent's container,
stop and ask rather than guessing an identity from repository history.

## Generated files stay generated

In `frontis`, `kancha`, and `website`, `DESIGN.md` is the source of truth for
design tokens; the generated token file (e.g. `src/constants/colors.ts`) is
built from it. Edit `DESIGN.md`, run `make tokens-export`, and check
`make tokens-check` — never hand-edit the generated file.

## One stack per repo, check before assuming

Don't pattern-match a sibling repo's stack onto this one. `xilo` uses `npm`
where its Cloudflare-Worker siblings (`azkena`, `plaza`) use `bun`; read the
repo's own `AGENTS.md` and lockfile before running a command that assumes
otherwise.

## Further reading

- `references/proving-a-change.md` — per-repo commands that actually prove a
  change works, not just that it compiles.
- `references/tells-of-a-wrong-claim.md` — moments you're about to overstate
  something in one of these repos, and the cheapest check for each.
- `references/agentic-automation.md` — the `gh aw`-based agent CI piloted in
  `frontis`; only relevant if you're asked to extend that pattern to another
  repo.
