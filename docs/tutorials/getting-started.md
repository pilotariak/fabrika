# Run your first agent with fabrika

This walks you through running the fabrika `lead` template against a real
Pilotariak repo, end to end: lead scopes the goal, creates a builder, the
builder reports back, and the lead creates a reviewer and QA agent against
the builder's branch. You'll see the whole loop once, on something small.

> **First run of this setup.** fabrika's templates reference themselves by
> their own `gh://Pilotariak/fabrika/...` URI, so none of this resolves
> until fabrika is merged to `main`. If you hit a resolution error, that's
> the first thing to check.

## Before you start

- A Scion Hub running on your machine: `scion server start` (if you haven't
  already — the onboarding wizard walks you through runtime detection and
  credentials).
- Harness auth configured for `codex` (the `lead` and `code-reviewer`
  default) and `claude` (the `builder` and `qa` default) — see
  [Harness Authentication](https://googlecloudplatform.github.io/scion/local/agent-credentials/).
- A git identity available to containers. The templates stop and ask rather
  than guessing one, so if `git config user.name`/`user.email` is empty for
  the agent, set it first.

## 1. Pick a small, low-risk repo

`xilo` or `azkena` are good first targets — small Cloudflare Worker repos,
quick to build and test. Avoid `kancha` for a first run: its QA path needs a
human with a device or simulator, which adds a step this walkthrough doesn't
cover.

```bash
cd ~/Projects/Pilotariak/xilo
scion init
```

`scion init` links this repo to your local Hub. Each agent you start here
gets its own git worktree of this checkout by default.

## 2. Start the lead

Give it something genuinely small — a one-line fix or a doc tweak is enough
to see the whole loop without burning much time on the actual change:

```bash
scion start lead-demo "Fix the typo in README.md's first paragraph" \
  --type gh://Pilotariak/fabrika/.scion/templates/lead
```

Watch it with `scion list` and `scion look lead-demo`, or attach directly
with `scion attach lead-demo` if you want to follow along interactively.

## 3. Watch the handoff happen

The lead should, on its own:

1. Read `xilo`'s own `AGENTS.md` and confirm no other agent is already
   running against this project (`scion list`).
2. Scope your one-line goal and start a builder:
   `scion start build-<slug> "..." --type gh://Pilotariak/fabrika/.scion/templates/builder`.
3. Wait for the builder to message back with a branch name and commit SHA.
4. On that message, start the reviewer and QA against that same branch:
   `--type .../code-reviewer --branch <branch>` and
   `--type .../qa --branch <branch>`.

Check `scion list` at each stage — you should see `lead-demo`, then a
`build-*` agent appear, then `review-*` and `qa-*` once the builder reports
in.

## 4. Read the reports, don't just trust the exit code

Once the reviewer and QA report back to the lead, read what they actually
say — a "looks good" with no commit named, or a "tests pass" with no command
named, is a tell something was skipped. The `pilotariak-context` skill's
`references/tells-of-a-wrong-claim.md` has more of these.

## 5. You decide what gets pushed

Nothing in this loop pushes a branch or opens a PR on its own — the lead's
own instructions stop short of that deliberately. Once you're satisfied with
what the builder, reviewer, and QA produced, push and open the PR yourself
(or ask the lead to, now that you've reviewed it).

## What you just saw

- A **template** is a directory (`scion-agent.yaml` + `agents.md` +
  `system-prompt.md`), referenced by `gh://` URI — no install step.
- Delegation is **agent-initiated**, not engine-enforced: the lead calls
  `scion start`/`scion message` itself, following the
  `software-engineering-process` skill's rules rather than a graph Scion
  evaluates for it.
- The **worktree-per-agent default** is what let the reviewer and QA see the
  builder's branch without anything being pushed.

Next: run the same loop with `pm` instead of `lead` if your goal needs the
14-role Chunky/Large tree, [add a new role template](../how-to/add-a-role-template.md)
if neither tree fits, or read
[why this setup looks the way it does](../explanation/why-scion.md).
