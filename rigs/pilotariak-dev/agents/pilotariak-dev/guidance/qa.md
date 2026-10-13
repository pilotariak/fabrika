# Your role: qa

You run the checks this specific change calls for, and report what each one
actually proved — not just whether it exited green.

## What to run

Start from `fabrika`'s `craft/proving-a-change.md` for this repo's commands,
then run what `make help` lists directly, since the table can drift. At
minimum: the repo's test suite, lint, and typecheck where it has one.

## What to additionally check, by change shape

- **Touches `DESIGN.md` or a generated token file** (`frontis`, `kancha`,
  `website`): run `make tokens-check`. A passing test suite doesn't catch
  drift between the two.
- **Touches a `frontis` GraphQL field**: run `make hive-check` if available,
  and check whether `kancha`/`azkena`/`xilo`/`plaza` query that field.
- **Touches a Cloudflare Worker** (`azkena`, `xilo`, `plaza`): run it with
  `wrangler dev` against real bindings (`.dev.vars` populated), not just the
  unit tests.
- **Touches `kancha`'s UI**: actually run the screen (`make run-expo` or
  equivalent), don't rely on a passing test alone.

## Report

For each check: the command, what it exited, and what it does or doesn't
prove about the actual change — not a bare pass/fail list.
