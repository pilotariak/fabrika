---
name: software-engineering-process
description: >-
  How multi-agent engineering work is owned, sized, delegated, and verified from request
  to delivery. Covers the ownership tree, the independent-review invariant, sizing by
  delegation depth, the review cycle, and briefing. Use when you are responsible for
  delivering an issue you will not implement yourself — classifying incoming work,
  deciding which roles to instantiate, creating and supervising agents, or governing a
  review cycle.
---

# Software Engineering Process

> Adapted from scion-frontiers/agent-team@3650f1b (Apache-2.0); modified for Pilotariak.

## Overview

How an engineering issue moves from request to delivered change when more than one agent is
involved: **who owns delivery, who creates whom, what each role owes, and when the work is
actually done.**

How to design, implement, and review is not here — read the role definitions and their own
skills for that.

## When to use

Use when you are **responsible for delivering an issue you will not implement yourself**:

- classifying an incoming issue and deciding how much process it warrants
- deciding which roles to instantiate, and creating those agents
- supervising agents you created and deciding whether to accept their output
- governing a review cycle
- deciding whether you are finished

**Do not use** as a developer, reviewer, architect, or investigator executing an assigned
task. Read your brief and your own role material instead.

## Mapping onto fabrika's templates

This skill's vocabulary (coordinator, dedicated issue owner, investigator, architect,
engineering manager, developer, reviewer) is generic, and fabrika's template names now
match it directly for the roles that exist. fabrika has two trees, and neither has every
generic role:

| Generic role              | fabrika Small tier                                   | fabrika Chunky/Large tree                                                                                     |
| ------------------------- | ---------------------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| Coordinator / issue owner | `coordinator`                                        | `coordinator` (same template, same agent — not a separate one per tree)                                       |
| Dedicated issue owner     | — (collapses into `coordinator`)                     | — (collapses into `coordinator`; see below)                                                                   |
| Investigator              | — (not used; collapses into `coordinator`'s scoping) | `investigator` — optional, created directly by `coordinator` for a goal that needs research first             |
| Architect                 | — (not used; collapses into `coordinator`'s scoping) | `architect` — optional, created directly by `coordinator` for a goal that needs a design doc first            |
| Engineering manager       | — (collapses into `coordinator`)                     | `eng-manager`                                                                                                 |
| Developer                 | `developer`                                          | `swe-1`, `swe-2`, `db`                                                                                        |
| Reviewer                  | `code-reviewer`, `qa-tester`                         | `code-reviewer`, `qa-tester`-equivalent split across `test-engineer`/`ui-test`/`perf-test`/`security-auditor` |

**fabrika's Chunky/Large tree is depth 2, not depth 3.** `coordinator` creates `po`/`eng-manager`
(and, when the goal calls for it, `investigator`/`architect`) directly; `eng-manager` creates
producers and verifiers directly. There is no dedicated-issue-owner layer between them —
`investigator` and `architect` exist as templates, but the top-level `coordinator` creates
them itself rather than delegating that creation to a dedicated sub-coordinator. Don't read
the generic §3 table below as implying fabrika has a three-layer tree; it doesn't, by
design, until a task shape actually forces that extra layer.

## 1. Ownership

**Exactly one agent owns delivery of an issue at any moment.** That agent is the
**issue owner**. By default the issue owner is the coordinator. The issue owner:

1. creates the agents it needs,
2. briefs them,
3. monitors them,
4. accepts or rejects what comes back,
5. remains the issue owner until the issue is delivered.

### How an issue gets its owner

An issue is owned in one of two ways, depending on size:

- **Small issues (depth 1):** the coordinator is the issue owner directly — it creates a
  developer and reviewer(s) and remains the issue owner itself. This corresponds to the
  Small tier in the Sizing table (§3) — fabrika's `coordinator`/`developer`/`code-reviewer`/`qa-tester` path.
- **Chunky and Large issues (depth 3 in the generic model):** the coordinator creates a
  **dedicated issue owner** — an agent using the coordinator template, briefed as a
  delegated sub-coordinator responsible for owning the SEP for that issue. The dedicated
  issue owner creates the roles the issue needs — investigator, architect, and engineering
  manager — and supervises them. **fabrika's Chunky/Large tree does not add this layer**:
  `coordinator` plays both coordinator and (when it doesn't collapse to Small) creates
  `investigator`/`architect` itself when the goal needs them, then hands straight to
  `eng-manager`.

In both cases, everything that follows about the issue owner applies equally: the
responsibilities are the same regardless of whether the coordinator fills the role itself
or delegates it to a dedicated agent.

**Only the top-level coordinator delegates issue ownership.** Within an issue, the issue
owner may delegate **implementation subtrees** — in fabrika, this is `eng-manager` owning
implementation and review for its producers and verifiers. `eng-manager` creates,
supervises, and accepts inside its subtree, but `coordinator` still accepts eng-manager's
subtree as a whole.

> **A dedicated issue owner owns the full process for its issue** — this is precisely
> what delegation of issue ownership means. What the coordinator retains is
> **project-level tracking**: it tracks all issues, monitors their dedicated owners, and
> remains responsible for the portfolio, not the per-issue process.

### Supervision follows creation

**The tree is the notification topology.**

| Mechanism                                                             | Consequence                                                |
| --------------------------------------------------------------------- | ---------------------------------------------------------- |
| Creating an agent **auto-subscribes** you to its notifications        | You are the one told when it completes or stalls           |
| Ancestry chains (`root → parent → child`) grant **transitive access** | Any ancestor can manage any descendant, at any depth       |
| Subscriptions can also be project-scoped                              | Non-creators *can* observe, but only if wired deliberately |

**You are the one who will be told when it breaks. If you look away, nobody is looking.**

**The agent that creates workers is the one subscribed to their notifications.** If you
delegate oversight to an agent that did not create the workers, nobody is subscribed —
it looks wired but is silently disconnected. This is why `eng-manager`, not a producer,
creates the verifiers that check that producer's work.

### Definition of done for an issue owner

> **Your work is not complete while your subtree is live. Delivery is the deliverable, not
> dispatch.**

Dispatching agents and reporting that you dispatched them is not completion. Signal
completion when the work is delivered, not when it is handed out.

## 2. The invariant

Scale everything down with the size of the work — **except independent review.**

> **For any issue under work, the author and the reviewer are never the same agent.**

The constraint is scoped to the issue, not the tree. A coordinator may supervise several
issues at once and does not count toward it.

The reviewer's job is *not letting bad code through*. An agent that just argued itself into
a design cannot hold that goal against its own work. This is role conflict, not context
size — a bigger context window does not fix it. This is the invariant `eng-manager`
enforces by always being the one that creates `test-engineer`, `ui-test`, `perf-test`,
`security-auditor`, and `code-reviewer` — never the producer whose work they check.

## 3. Sizing

Sizing is **depth of the ownership tree** — not stage count, not agent count. This table is
the generic model this skill defines; see "Mapping onto fabrika's templates" above for what
fabrika actually implements (depth 1 or depth 2, never depth 3).

| Tier   | Tree                                                                                                                                                       | Depth |
| ------ | ---------------------------------------------------------------------------------------------------------------------------------------------------------- | ----- |
| Small  | coordinator → developer, reviewer                                                                                                                          | 1     |
| Chunky | coordinator → dedicated issue owner<br>dedicated issue owner → investigator, architect, engineering manager<br>engineering manager → developers, reviewers | 3     |
| Large  | coordinator → dedicated issue owner<br>dedicated issue owner → investigator, architect, engineering manager<br>engineering manager → developers, reviewers | 3     |

The coordinator is the root at every size. Sizing is how many layers hang beneath it.

> **Note:** Chunky and Large are structurally identical in tree shape and depth.
> Both place a dedicated issue owner between the coordinator and the working roles.
> The distinction between Chunky and Large is **scope and complexity of the issue**,
> not tree structure — both warrant a dedicated issue owner, but a Large issue
> typically involves more phases, more agents, or longer timelines than a Chunky one.
> fabrika's Chunky/Large tree covers what this generic model calls Chunky/Large without
> adding the dedicated-issue-owner layer — see the mapping above.

**Depth is not change magnitude and not dispatch route.** Those are separate axes, they
carry their own labels elsewhere, and those labels overlap with these tiers. They also
correlate — a depth-3 tree is usually a large change — which is exactly what makes merging
them look safe. It is not: a large change can still be one developer and one reviewer, and
neither axis fixes the depth of the tree. All three may legitimately coexist. **Do not
consolidate them into this one.**

Changing the look of a single button does not need an investigator, an architect, an
engineering manager, developers *and* reviewers. It needs a developer and a reviewer — in
fabrika, `coordinator` collapsing straight to a `developer`.

### Sizing rules

1. **Size up when uncertain.** Estimation is unreliable; over-sizing is the cheaper error.
2. **Every orchestrating role must collapse itself when handed less than its tier
   assumes.** `eng-manager` given a trivial change relays it to a single producer instead
   of manufacturing a producer/verifier fan-out it doesn't need.

**Never apply rule 1 without rule 2.** Over-sizing without collapse produces **ceremony
inflation**: an agent told it is `eng-manager` runs the full five-verifier fan-out, because
that is what the role description says the role does.

### Cost of supervision

A coordinator sustains roughly **2–3 issues concurrently**, and **tens of agents across a
session**. It scales because it holds very little detail per issue: it supervises rather
than does.

> **Do not carry per-issue detail you can re-read.** Design rationale, review findings and
> implementation state belong in artifacts and scratchpad files. Go read them when you
> need them.

Holding that state yourself is what takes you from three concurrent issues down to one.

## 4. The work

Roles under supervision, each owing an artifact to the agent that created it. Instantiate
only the roles the size calls for.

| Role                                                                                                       | Receives                   | Owes                                                                                                    |
| ---------------------------------------------------------------------------------------------------------- | -------------------------- | ------------------------------------------------------------------------------------------------------- |
| Investigator (`investigator` in fabrika, optional, created directly by `coordinator`)                      | the issue                  | findings — facts, no design decisions                                                                   |
| Architect (`architect` in fabrika, optional, created directly by `coordinator`)                            | the investigation findings | an agreed design, iterated with a human. Reads the findings; does not re-research                       |
| Engineering manager (`eng-manager` in fabrika)                                                             | the agreed design          | implementation phases; the parallel-vs-sequential call; a supervised subtree of producers and verifiers |
| Developer (`developer`/`swe-1`/`swe-2`/`db` in fabrika)                                                    | a brief and a phase        | a change, ready for review                                                                              |
| Reviewer (`code-reviewer`/`qa-tester`/`test-engineer`/`ui-test`/`perf-test`/`security-auditor` in fabrika) | a change                   | a verdict, held to *do not let bad code through*                                                        |

The issue owner monitors each of these and accepts or rejects the artifact. An artifact
that comes back thin, off-brief, or stalled is the issue owner's problem to catch.

## 5. The review cycle

Run per **pull request or per developer** — not across the whole branch at once. A final
whole-branch review is sometimes warranted, but never as a replacement for the per-unit
cycle.

Each round: review → fixes → **new reviewer**. Repeat until a review comes back clean.

### Rotate the reviewer, keep the developer

|           | Across rounds | Why                                                                                           |
| --------- | ------------- | --------------------------------------------------------------------------------------------- |
| Reviewer  | **replaced**  | Sheds context. Avoids anchoring on its own prior review; attention decays across long reviews |
| Developer | **retained**  | Keeps context. Holds the thread of what was tried and why                                     |

**Compact the adversary, preserve the author.** Do not generalise this to "fresh context is
always better" and start rotating developers. In fabrika this means a review finding goes
back to the producer that wrote the change, never to a newly spawned fix agent.

A reviewer re-reading its own review commonly misses items it raised itself.

**Cap: 6 rounds**, then escalate. Hitting the cap regularly means something upstream is
broken.

To escalate, hand your supervisor the round history, the unresolved findings,
what was already tried, and a recommended next action. **Do not open a seventh round
without a decision.**

**Non-blocking is not optional.** It means "will not block the merge review." It does not
mean "skip it before shipping."

Every finding ends in **resolved** or **explicitly declined as not required**. Postponing an
accepted finding needs a human decision and a tracked follow-up — a reason written in the
review is not a disposition.

## 6. Context discipline

Separate agents per phase **are** separate context windows. **Do not collapse phases into
one agent to save time** — what you lose is the context reset, not just the division of
labour.

- A role boundary is a context reset.
- A fresh reviewer is a fresh window.
- Per-PR review scope keeps attention bounded.

Agent performance degrades well before the context window is full. Treat roughly **40%
utilisation** as the point where quality starts costing more than the saved handoff.

## 7. Briefing

**Every agent you create needs a brief, and no agent starts work without one.** An agent
you created that is doing the wrong thing is usually an agent you briefed badly.

For brief structure and file-based handoff, see `scion-agent-manage` (Scion's own platform
skill). For a `coordinator`-to-`eng-manager` brief specifically, see
`references/eng-manager-brief-template.md` in this skill.

## 8. Common rationalizations

| Rationalization                                                 | Reality                                                                                                                                                                                   |
| --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "I'm `eng-manager`, so I should fan out all five verifiers."    | A trivial change goes straight to one producer and the verifier the change shape calls for. Orchestration you did not need is waste you introduced.                                       |
| "I'm `coordinator`, so I owe a design document."                | Handed a one-line fix, you owe a sentence and a decision — and a plain `developer`, not the full tree.                                                                                    |
| "This change is small enough that I can review my own work."    | You cannot hold both "make this work" and "do not let this through."                                                                                                                      |
| "I handed it off, so it's their problem now."                   | You delegated the work, not the delivery.                                                                                                                                                 |
| "My task was to dispatch the producers, and I dispatched them." | Delivery is the deliverable, not dispatch. You are not done while your subtree is live.                                                                                                   |
| "I spawned the agents, so I'm done until they report."          | An agent that never reports is exactly the case you exist to catch. Silence is not progress.                                                                                              |
| "The last reviewer approved it, so the follow-up fix is fine."  | Fresh reviewer each round. Reviewers miss items on re-read of their own review.                                                                                                           |
| "These findings are non-blocking, so I'll ship and fix later."  | Non-blocking means it will not block merge review. It does not mean skip.                                                                                                                 |
| "I'll review the whole branch at once, it's more efficient."    | Per-PR or per-developer. Whole-branch reviews dilute the attention the cycle depends on.                                                                                                  |
| "I already know how to review code / plan work / design this."  | You have generic priors, not this project's methodology. Read the role material.                                                                                                          |
| "The brief is long, so I'll pass it inline."                    | Write it to the scratchpad and pass the filepath — an inlined brief puts backticks and `$variables` into a shell command. See `scion-cli-operations` → **Shell Safety for Task Prompts**. |

## 9. Red flags

- You created an agent and have not heard from it, and have not gone to check.
- You are about to report completion while an agent you created is still running.
- The same agent wrote and reviewed a change.
- A reviewer is reviewing its own previous review's fixes.
- `coordinator` is re-running scoping that `eng-manager` already did.
- You are relaying messages between two agents that could talk directly.
- `eng-manager` is running the full five-verifier fan-out against a one-line fix.
- Review findings were recorded as non-blocking and never revisited.

## 10. Verification

Before reporting delivery:

- [ ] Every agent you created has reported, been accepted, or been explicitly stopped.
- [ ] Every change was reviewed by an agent that did not write it.
- [ ] The final review round came back clean — not "clean except".
- [ ] Every non-blocking finding was resolved or explicitly declined. Nothing accepted is
      still pending.
- [ ] No subtree is still live.

**Run this immediately before you report — not as you go.** A box ticked earlier states
something about a moment that has passed: agents you created have since finished, stalled,
or spawned others. **Re-check; do not recall.**

## 11. Related material

| For                                                                | See                                                        |
| ------------------------------------------------------------------ | ---------------------------------------------------------- |
| `coordinator`-to-`eng-manager` brief template                      | `references/eng-manager-brief-template.md` in this skill   |
| Shell metacharacters and absolute paths in task prompts            | `scion-cli-operations` → **Shell Safety for Task Prompts** |
| Briefing structure, model override, agent recovery, stall handling | `scion-agent-manage`                                       |
| Review methodology and severity                                    | `code-review` (this pack)                                  |
| How a produced artifact survives the agent that made it            | `artifact-durability` (this pack)                          |
