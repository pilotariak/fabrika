# pilotariak-dev

A four-seat OpenRig team (lead, builder, code-reviewer, QA) for working in any
Pilotariak repo: `frontis`, `kancha`, `azkena`, `xilo`, `plaza`, `website`,
`decisions`, `infrastructure`. Install it once as `pilotariak-dev` and launch
it in whichever repo's clone you're working on.

| Seat                        | Does                                                                                |
| --------------------------- | ----------------------------------------------------------------------------------- |
| `orch-lead@pilotariak-dev`  | Takes your goal, scopes it, hands it to the builder, keeps the PR description true. |
| `dev-build@pilotariak-dev`  | Makes the change on its own branch and proves it works.                             |
| `dev-review@pilotariak-dev` | Reviews the handed-off commit against the repo's own maps and the code.             |
| `dev-qa@pilotariak-dev`     | Runs the checks the change calls for and reports what each proved.                  |

`CULTURE.md` holds the team's values, including the Pilotariak-specific ones
(DCO sign-off, the `frontis`-is-the-hub caution, generated-token-file
discipline). Each seat's role is in `agents/pilotariak-dev/guidance/`.

## Deliberately lean

This bundle doesn't ship a borrowed skill tree. A fuller OpenRig team bundle
typically carries a couple dozen generic operating skills (mission/slice
bookkeeping, compaction restore, queue handoff, …) alongside its roles — real
infrastructure for running several concurrent missions across restarts. This
one doesn't have that: each seat gets its role's guidance, the shared startup
steps, and the `fabrika` context pack. That's a real tradeoff, not free
simplicity — for sustained, multi-week work you'll feel the lack of a
mission/slice ledger. For "scope one change, build it, review it, ship it" on
a solo-maintained multi-repo org, it's enough, and it's small enough to read
end to end and own.

There's no `world-bundle.yaml` here either: that file exists elsewhere as a
preserved copy of an older bundle format from before a schema migration. This
bundle was authored fresh against the current format, so `rig.yaml` is the
only spec file — there's nothing to preserve a duplicate of.

## Before you start

- OpenRig 0.6.6 or later (`rig --version`), Node 22 or 24, and tmux.
- Claude Code and/or Codex installed and logged in — `recommended` uses both;
  `all-claude`/`all-codex` need only one.
- A clone of the Pilotariak repo you want to work on, set up the way its own
  `README`/`AGENTS.md` says.
- A GitHub account with push access, if you intend to publish the pull
  request this team prepares.

Keep OpenRig's files out of your commits. From the root of your clone:

```bash
printf '%s\n' CLAUDE.local.md AGENTS.md /.openrig/ >> .git/info/exclude
```

This file is local to your clone and never committed. The lead adds any
missing entries itself if you skip this step.

Add the `fabrika` context pack once per machine:

```bash
rig context add --git https://github.com/Pilotariak/fabrika.git
```

## Run it

From the root of the repo clone you want to work on:

```bash
rig bundle install <path-or-url-to>/pilotariak-dev --preset recommended --target ~/rigs/pilotariak-dev --cwd . --plan
rig bundle install <path-or-url-to>/pilotariak-dev --preset recommended --target ~/rigs/pilotariak-dev --cwd .
```

The first command previews the install; the second installs and launches it.
For one provider, use `all-claude` or `all-codex` instead of `recommended`.
Each configuration uses the **same** `--target ~/rigs/pilotariak-dev`.

When working from a clone of this repository (`fabrika`), preview the authored
spec directly:

```bash
rig up rigs/pilotariak-dev/rig.yaml --cwd . --plan
```

**Reaching the team.** The rig is named `pilotariak-dev` unless launched under
another name (`rig ps` lists your rigs). Send the lead a goal:

```bash
rig send orch-lead@pilotariak-dev "Please take issue #<number>: <one line on what you want>"
```

Stop the team with `rig down pilotariak-dev`. That removes OpenRig's blocks
from `CLAUDE.local.md`/`AGENTS.md`; git worktrees under `.worktrees/`, if any,
stay until you remove them yourself.

## Permissions

No `permission_policy` is set here, so every seat launches under its
runtime's normal prompts: Claude Code seats ask before file edits and most
commands follow your Claude Code rules; Codex seats run with
`workspace-write` (no network, including the local OpenRig daemon, unless you
opt a seat into a networked config profile — see OpenRig's own
`docs/reference/getting-started.md` for that). This is the safer default and
the one this bundle ships.

If you want the faster, prompt-free posture some OpenRig teams default to
(Claude Code's `--dangerously-skip-permissions`, Codex's full-access mode),
add to `rig.yaml`:

```yaml
permission_policy: builtin:yolo
non_interruptive: true
```

That means every seat can change or delete files you can reach, install
packages, and use the network, without asking, for as long as the team runs.
Only add it on a machine where you're comfortable with that, and only after
reading what each runtime's bypass flag actually does.

**The team still asks for your word before it publishes**, either way — the
lead pushes a branch, opens a pull request, or comments only when you say so
(`CULTURE.md`). With prompts off, nothing enforces that beyond the seats
following their own instructions.

## Limits

- **A starter, not a process.** The team prepares one pull request at a time.
  Merging, releases, and multi-repo coordination stay with you.
- **Four seats share one clone**, each in its own worktree if it builds —
  budget the disk and time for that.
- **No mission/slice ledger.** Continuing work across a restart isn't tracked
  for you; re-brief the lead on where things stand if a seat restarts
  mid-task.
- **Every guidance file here can be stale.** The repo's own code and CI
  decide, not this bundle.
