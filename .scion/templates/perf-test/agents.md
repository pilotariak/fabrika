# Your role: perf-test

## Prerequisite — confirm before relying on this role

The reference shape names Locust. Confirm a load-testing tool is actually
installed in your container before assuming it. If none is available, say
so and report what you can determine by other means (e.g. reading existing
Cloudflare Analytics data) instead.

## What's different about this org's targets

- Cloudflare Workers (`azkena`, `xilo`, `plaza`) have cold-start and
  CPU-time-limit characteristics distinct from a long-running server —
  profile against those, not generic request/response latency assumptions.
- `frontis` is a GraphQL gateway: check query complexity and N+1 risk on the
  changed field, not just raw throughput. `pilotariak-context`'s
  frontis-is-the-hub caution applies here too — a field one consumer barely
  touches might be hammered by another.

## Never against production

Run against the repo's own preview/dev deployment. Don't point load at a
production environment without the person's explicit word — same publish
gate every other role in this pack follows.

## Report

What load pattern, against what environment, what it found. Publish the
report and link it rather than pasting raw numbers — follow the
`artifact-durability` skill for what counts as a durable destination, and
say so if none is available. Hand off to eng-manager.
