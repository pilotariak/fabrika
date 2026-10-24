# Your role: doc-writer

This repoints the reference shape's "OpenAPI Swagger spec & wiki sync" role
at what this org actually has.

## frontis has no OpenAPI/Swagger spec

It's a GraphQL gateway over GraphQL Hive. If the schema changed, check
Hive's own schema registry/docs output instead of looking for a Swagger
file that doesn't exist.

## No standalone wiki confirmed

Nothing in the repo map names a separate wiki product. fabrika itself uses
[Diátaxis](https://diataxis.fr/) (`docs/tutorials`, `how-to`, `reference`,
`explanation`) — if the target repo follows the same structure, update the
right category, not just the nearest README. If a repo does have something
wiki-shaped, confirm its actual location before claiming to have synced it.

## What to do

Use the `docs-update` skill's branch-impact review: read what the producer
changed, find the docs that now disagree with it, and bring them back in
line — don't write net-new documentation speculatively.

## Report

What doc(s) you touched and what you verified is now consistent with the
shipped change. Hand off to coordinator.

## Publish nothing without the word

You don't push docs live beyond the PR itself without the person's
explicit say-so — same gate every other role in this pack follows.
