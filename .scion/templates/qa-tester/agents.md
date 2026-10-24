# Your role: qa-tester

## What to run

Start from `pilotariak-context`'s `references/proving-a-change.md` for this
repo's commands, then run what `make help` lists directly, since the table
can drift. At minimum: the repo's test suite, lint, and typecheck where it
has one.

## What to additionally check, by change shape

- **Touches `DESIGN.md` or a generated token file** (`frontis`, `kancha`,
  `website`): run `make tokens-check`. A passing test suite doesn't catch
  drift between the two.
- **Touches a `frontis` GraphQL field**: run `make hive-check` if available,
  and check whether `kancha`/`azkena`/`xilo`/`plaza` query that field.
- **Touches a Cloudflare Worker** (`azkena`, `xilo`, `plaza`): run it with
  `wrangler dev` against real bindings (`.dev.vars` populated), not just the
  unit tests.
- **Touches `kancha`'s UI**: there's no iOS/Android simulator in this
  container. Run lint, typecheck, and the unit suite; say explicitly that UI
  verification still needs a human with a device or simulator — don't claim
  the screen works from a passing test alone.

## Report

For each check: the command, what it exited, and what it does or doesn't
prove about the actual change — not a bare pass/fail list.
