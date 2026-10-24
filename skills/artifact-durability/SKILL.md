---
name: artifact-durability
description: >-
  Where a produced artifact has to end up so that it survives the agent that produced it,
  and what to do when no durable destination is available. Owns the property definition
  (reachable after your container is deleted), the list of durable destinations, and the
  fallback ladder. Use when you are about to write a deliverable to a path, when you are
  deciding whether your work is safe, or before you signal completion.
---

# Artifact Durability

> Adapted from scion-frontiers/agent-team@3650f1b (Apache-2.0); modified for Pilotariak.

## The property

> **An artifact is durable when it is reachable after your container has been deleted.**

Not written. Not saved. Not "in the scratchpad." Reachable by someone else, later, once you
are gone.

You will not be consulted before your container is deleted, so every check you run on your
own work has to test that property and nothing weaker.

## Why this rule is stated as a property and not a place

Each of these checks tests something *easy to observe* instead of the thing that
*determines survival*:

| The check that was written                    | What it actually tests  | What survives                         |
| --------------------------------------------- | ----------------------- | ------------------------------------- |
| "don't delete an agent with uncommitted work" | a commit exists locally | the commit is **pushed**              |
| "committing prevents losing work"             | a commit exists locally | the commit is **pushed**              |
| "or written to the scratchpad"                | a **path** was used     | the path is **outside the container** |

**A location satisfies a check. Only a property satisfies the purpose.** A path is evidence
of durability only when you know why that path outlives you.

## Durable destinations

Scion's own platform rule (`scion-agent-manage` → **Agent Lifecycle**) is explicit and
leaves no room to assume otherwise: **"A commit that was never pushed is local to the
container and dies with it — committed is not the same as pushed."** Exactly three things
survive your deletion:

1. **A pushed git ref on a remote.** Push, then confirm the remote has it. A commit that
   never left the container dies with it, and it passes an uncommitted-work check on the
   way out.
2. **Storage mounted from outside the container** — the shared scratchpad volume Scion
   provides for a project, or a hosting bucket you have published to and verified. Durable
   because the deletion cannot reach it, not because of how the path is spelled.
3. **The body of a message you have already sent.** Content inline in the message, not a
   filepath pointing at storage only you can see.

Everything else dies with you: the working tree, an unpushed commit, `/tmp`, a gitignored
file, and any container-local scratch directory.

## The tension with fabrika's own publish gate — and how it's resolved

`coordinator` says "the person decides what gets pushed," and fabrika's
worktree-per-agent default lets a reviewer or verifier see a producer's commit without
anything being pushed. Read against the rule above, that commit is **not yet durable** —
it is local to the producer's container (or the shared worktree, which is still inside the
project's live infrastructure, not pushed anywhere) until someone pushes it or it is never
pushed at all.

This is not a contradiction to paper over; it is a real gap, and it is closed by a
lifecycle rule, not by inventing a new durable destination: **the agent that created you
must not delete you — or any agent sharing your unpushed branch — until the person has
decided whether to push it or explicitly said the work can be discarded.** Scion's own
"Deleting an agent with unpushed work" anti-pattern (`scion-agent-manage` → **Agent
Lifecycle**) names exactly this failure mode. Concretely:

- A producer does not push its own branch — pushing is `coordinator`'s call, following
  its own publish-gate rule.
- `coordinator`/`eng-manager` do not delete a producer, reviewer, or verifier whose work is still
  the subject of an open review cycle, even once it reports completion — a completion
  signal is not deletion permission (`scion-agent-manage` → **Agent Lifecycle**, rule 1).
- Until the person has pushed the branch (or explicitly says it's fine to lose), your
  commit's survival depends on nobody having deleted it yet. That is fragile, not durable —
  say so if you are ever asked to confirm your work is safe before the person has acted.
- `scion delete --preserve-branch` keeps a branch around after its agent is deleted, but
  the flag itself warns it does not push — confirm the branch reached the remote before
  relying on it for anything beyond a short review window.

## When only container-local storage is available

A container-local scratch directory is a real fallback for scratch, and an environment with
no shared volume is real. It is **not** a delivery destination, for two reasons — and the
second is the one that gets missed:

- it does not survive your container, and
- it is **not shared with any other agent**, so it cannot carry a handoff either.

If the shared volume is unavailable, do not write your deliverable there and signal
completion. Do the first of these that is available to you:

1. **Commit it to your own branch and push it**, if pushing is yours to do for this role —
   for most fabrika roles it is not; see the gate above. Where it isn't, this option isn't
   available to you either: fall through to the next one.
2. **Send the content in a message** to the agent that dispatched you, inline in the body.
   Say in the message that this is the delivery, because no durable file exists.
3. **Declare a blocker** and withhold the completion signal until it is resolved.

Never settle this silently by picking whichever location is easiest to write to. Choosing a
non-durable path without saying so looks identical, from outside, to having delivered.

## A gitignored deliverable is invisible to "commit and push"

With a scratch directory in `.gitignore`, a file written under it leaves `git status`
reporting a **clean tree** and `git add -A` staging **nothing**.

So a completion rule of the form *"commit and push anything still sitting in the working
tree"* cannot see such a deliverable, and does not fail — it reports success. If your
deliverable is a file rather than a commit, name that file and confirm its destination
directly. Do not infer that it is safe from a clean working tree.

## Hardening against one hazard is not durability

When you protect an artifact, **name the hazard you did not cover.** Adding a file to
`.gitignore` protects it from `git clean` — a real fix for a real incident — while leaving
it exposed to container deletion, and while actively preventing it from being committed.
Two hazards; that entry closes one and deepens the other.

Protecting something against the hazard you just met makes it *feel* protected, and that
feeling is what stops the search for the second hazard.

## Signal completion honestly about what is and isn't pushed

A completion signal is widely read as a cue that cleanup (deletion) can follow. For most
fabrika roles, you are not the one who pushes — so "signal completion only after you have
pushed" does not apply to you directly. What you owe instead: name the exact branch and
commit SHA in your handoff message, confirm the commit actually exists where you say it
does (`git log`/`git status` — not just that your own `git commit` command exited zero),
and say plainly that it is not yet pushed. That tells whoever created you — bound by the
rule above — not to delete you or your worktree until the person has acted. If you produced
no changes there is nothing to report as pending — do not manufacture a commit. If a role
you're in *is* the one responsible for pushing (none are, by default, in this pack), push
every commit that is not yet on the remote before you signal completion, and treat a push
failure as a blocker, not as done.

## When your deliverable is not a commit

Some roles produce deliverables that live outside any repo working tree — load-test
reports, QA screenshots, health/SLO dashboards, state files. These go to the shared
scratchpad volume or inline into a message, not into a branch, so `git status` and
`git add -A` will never mention them. If such a deliverable ever lands on a gitignored path
instead, those commands will report a clean tree and you will read that as done.

A push covers your branch; it does not cover a file-based deliverable. Before you signal
completion, confirm the deliverable itself is somewhere your container's deletion cannot
reach. If the shared volume is unavailable, see **When only container-local storage is
available** above.

## Related

- Who decides what gets pushed to the GitHub remote, and when: `coordinator`'s own
  "before you publish" rule (this pack's templates).
- Who may delete an agent, and what must be true first, including the unpushed-work
  anti-pattern this skill builds on: `scion-agent-manage` → **Agent Lifecycle** (Scion's
  own platform skill).
