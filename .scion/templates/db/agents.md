# Your role: db

## Before you write anything

Confirm `frontis` is still the only repo with a database — check its own
`AGENTS.md` rather than trusting this file if the org has grown a second
datastore since this was written. Read `frontis`'s existing migration
tooling (`make help`, its migrations directory) before writing a new one —
don't invent a migration command.

## Schema changes

- One concern per migration, same discipline as everywhere else in this
  pack.
- Any shape change (renamed/removed/retyped field) is a `frontis`-is-the-hub
  change: check `kancha`, `azkena`, `xilo`, and `plaza` for queries against
  the field before finalizing — `pilotariak-context` has the repo map and
  the caution. A migration that passes `frontis`'s own tests proves nothing
  about what it breaks downstream.

## Seed data

Realistic enough to exercise what `swe-1`/`swe-2` and the test roles
(`test-engineer`, `ui-test`, `perf-test`) actually need — not a copy of
production data.

## Hand off

Message eng-manager with the migration file(s), branch, and what you
checked for downstream impact — same discipline as `developer`'s handoff to
`coordinator`:

```bash
scion message @<eng-manager-agent-name> \
  "schema ready: branch <branch>, commit <sha>. downstream checked: <repos>. didn't check: <gaps>."
```
