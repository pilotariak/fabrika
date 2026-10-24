# Run your first agent with fabrika

This walks you through running the fabrika `coordinator` template against a
real Pilotariak repo, end to end: coordinator scopes the goal, creates a
developer, the developer reports back, and the coordinator creates a
reviewer and QA agent against the developer's branch. You'll see the whole
loop once, on something small.

> **First run of this setup.** fabrika's templates reference themselves by
> their own `gh://Pilotariak/fabrika/...` URI, so none of this resolves
> until fabrika is merged to `main`. If you hit a resolution error, that's
> the first thing to check.

## Before you start

- A Scion Hub running on your machine: `scion server start` (if you haven't
  already — the onboarding wizard walks you through runtime detection and
  credentials).
- Harness auth configured for `codex` (the `coordinator` and `code-reviewer`
  default) and `claude` (the `developer` and `qa-tester` default) — see
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

## 2. Start the coordinator

Give it something genuinely small — a one-line fix or a doc tweak is enough
to see the whole loop without burning much time on the actual change:

```bash
scion start coordinator-demo "Fix the typo in README.md's first paragraph" \
  --type gh://Pilotariak/fabrika/.scion/templates/coordinator
```

Watch it with `scion list` and `scion look coordinator-demo`, or attach
directly with `scion attach coordinator-demo` if you want to follow along
interactively.

## 3. Watch the handoff happen

The coordinator should, on its own:

1. Read `xilo`'s own `AGENTS.md` and confirm no other agent is already
   running against this project (`scion list`).
2. Scope your one-line goal and start a developer:
   `scion start build-<slug> "..." --type gh://Pilotariak/fabrika/.scion/templates/developer`.
3. Wait for the developer to message back with a branch name and commit SHA.
4. On that message, start the reviewer and QA against that same branch:
   `--type .../code-reviewer --branch <branch>` and
   `--type .../qa-tester --branch <branch>`.

Check `scion list` at each stage — you should see `coordinator-demo`, then a
`build-*` agent appear, then `review-*` and `qa-*` once the developer
reports in.

## 4. Read the reports, don't just trust the exit code

Once the reviewer and QA report back to the coordinator, read what they
actually say — a "looks good" with no commit named, or a "tests pass" with
no command named, is a tell something was skipped. The `pilotariak-context`
skill's `references/tells-of-a-wrong-claim.md` has more of these.

## 5. You decide what gets pushed

Nothing in this loop pushes a branch or opens a PR on its own — the
coordinator's own instructions stop short of that deliberately. Once you're
satisfied with what the developer, reviewer, and QA produced, push and open
the PR yourself (or ask the coordinator to, now that you've reviewed it).

## What you just saw

- A **template** is a directory (`scion-agent.yaml` + `agents.md` +
  `system-prompt.md`), referenced by `gh://` URI — no install step.
- Delegation is **agent-initiated**, not engine-enforced: the coordinator
  calls `scion start`/`scion message` itself, following the
  `software-engineering-process` skill's rules rather than a graph Scion
  evaluates for it.
- The **worktree-per-agent default** is what let the reviewer and QA see the
  developer's branch without anything being pushed.

Next: give the same `coordinator` a goal that needs the full 14-role
Chunky/Large tree instead of a Small one,
[add a new role template](../how-to/add-a-role-template.md) if neither
tree fits, or read
[why this setup looks the way it does](../explanation/why-scion.md).
