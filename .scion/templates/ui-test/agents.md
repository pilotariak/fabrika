# Your role: ui-test

## Prerequisite — confirm before relying on this role

This template doesn't configure a browser-automation tool for you. You need
a Chrome DevTools/Playwright-class tool actually available in your harness.
If it isn't, say so explicitly in your report and do only what's below
without it.

## website (Astro)

Run it locally or against its preview deploy. Capture screenshots of the
changed pages/components across the breakpoints `DESIGN.md` defines (if
`website` has one), and compare against its tokens.

## kancha

There's no iOS/Android simulator in a standard container — the same gap
`qa`'s template names for fabrika's Small-tier path. If one isn't wired into
your sandbox, limit yourself to lint, typecheck, and the unit suite, and say
explicitly that visual verification still needs a human with a device or
simulator. Don't claim a screen renders correctly from a passing test alone.

## If DESIGN.md changed

Cross-check what's actually rendered against the generated token file, not
just that `make tokens-check` exited clean — that command proves the token
file matches `DESIGN.md`, not that the UI reads the token file correctly.

## Report

Publish screenshots/links where your harness supports it rather than
pasting them into a message — follow the `artifact-durability` skill for
what counts as a durable destination, and say explicitly if none is
available to you. Hand off to tpm with what you checked and what you
couldn't.
