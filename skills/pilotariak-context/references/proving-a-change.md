# Proving a change, per repo

`make help` is the entry point in every repo that has a Makefile — run it
first; it's more current than this table, which will drift. This records the
commands that matter when you've just changed code and need to show it
works, not just that it compiles.

| Repo             | Prove it works                                              | Also run if touched                                                                                                                                                                   |
| ---------------- | ------------------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `frontis`        | `make test`, `make typecheck`                                | `make hive-check` if you changed the GraphQL schema (validates against the Hive registry before `hive-publish`); `make tokens-check` if you touched `DESIGN.md`; a `migrate:local` + `db:reset:<league>:local` round-trip if you changed a migration |
| `kancha`         | `bun test` (or `npm test`), `make tokens-check`               | `make run-expo` / `make run-ios` / `make run-android` to see the actual screen, not just green tests — this repo's own `AGENTS.md` requires exercising the UI before calling a frontend change done. No simulator exists in a Linux container: scope automated checks to lint/typecheck/unit tests, and say explicitly that UI verification still needs a human with a device or simulator. |
| `azkena`         | `make test`, `make lint`                                     | `make dev` and call the MCP tool by hand if you changed a tool's shape — a passing unit test doesn't prove the tool still round-trips through an MCP client                              |
| `xilo`           | `make test`, `make lint`, `make types`                        | `make dev` + a real Slack slash command if you changed a handler; this repo uses `npm`, not `bun`, unlike its Cloudflare-Worker siblings                                                 |
| `plaza`          | check its own README/Makefile — minimal local tooling as of this writing, most logic lives under `workers/` | —                                                                                                                                                                                        |
| `website`        | `make check`, `make lint`                                     | `make tokens-check` if you touched `DESIGN.md`; `make preview` to look at the rendered page                                                                                              |
| `decisions`      | `pre-commit run --all-files` (Markdown-only — the ADR content itself is the artifact) | —                                                                                                                                                                                         |
| `infrastructure` | no `AGENTS.md`/`Makefile` surveyed yet — read what's there before assuming any convention from this table applies | —                                                                                                                                                                                         |

## What "proven" means here, not just "tests pass"

A green test suite proves the unit you tested. It does not prove:

- that a `frontis` schema change doesn't break a `kancha`/`azkena`/`xilo`/`plaza`
  query against it — check the downstream repo, or at least grep its GraphQL
  documents for the field you changed;
- that generated files are regenerated — `src/constants/colors.ts` in
  `kancha` (and the equivalent in `website`) is generated from `DESIGN.md`;
  editing one without running `tokens-export`/`tokens-check` leaves them
  silently out of sync until CI's `design-lint.yml` catches it;
- that a Cloudflare Worker change works against real bindings — `wrangler
  dev` locally, with `.dev.vars` populated, before trusting `make deploy`.
  Deploy credentials are the builder's concern if a preview deploy is part of
  proving the change; a reviewer or QA pass should not need them.
