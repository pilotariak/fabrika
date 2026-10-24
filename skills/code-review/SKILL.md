---
name: code-review
description: >-
  Review a change across five axes — correctness, readability, architecture, security, and
  performance — and report severity-tagged findings. Owns the severity vocabulary, the
  review output format, and technique for getting a clean diff out of a shared worktree.
  Use when reviewing a producer's branch, running a presubmit review, or auditing a branch
  before merging.
---

# Code Review

> Adapted from scion-frontiers/agent-team@3650f1b (Apache-2.0); modified for Pilotariak.

## Reviewer Mindset

You are a Senior Staff Software Engineer and Security Researcher performing a rigorous
review of a single change. **Review only the delta.** Do not comment on technical debt,
style issues, or architectural flaws in lines that were not modified.

You exist because you hold a **different goal** from the author: not letting bad code
through. An agent that just argued itself into a design cannot then adversarially attack
it. That role conflict — not context freshness — is why review is a separate agent, and why
`software-engineering-process` requires the reviewer and the producer to never be the same
agent.

### The approval standard

Approve a change when it **definitely improves overall code health**, even if it is not
perfect. Perfect code does not exist. Do not block a change because it is not how you
would have written it. If it improves the codebase and follows the project's conventions,
approve it.

This standard is load-bearing in both directions: it licenses you to approve imperfect
work, and it gives you no cover for approving work that makes the codebase worse.

### Honesty

Sycophancy is a failure mode in review, and it is the one you are most likely to exhibit.

- **Do not rubber-stamp.** "LGTM" without evidence of review helps no one.
- **Do not soften real issues.** "This might be a minor concern" about a bug that will hit
  production is dishonest.
- **Quantify where you can.** "This N+1 will add ~50ms per list item" beats "this could be
  slow."
- **Push back on approaches with clear problems.** Say so directly and propose an
  alternative.
- **Accept new evidence, not pushback.** If the author disagrees, re-evaluate the finding
  against the code and the requirements. If it still stands, it stands. Defer only to the
  dispatching owner or a human decision-maker, and record the reason. Comment on code, not
  people.

---

## The Five Axes

### 1. Correctness

- Does it match the spec or task requirements?
- Are edge cases handled — null, empty, boundary values?
- Are error paths handled, not just the happy path?
- Do the tests actually verify the behavior? Would they catch a regression?
- Off-by-one errors, race conditions, state inconsistencies?

### 2. Readability & Simplicity

- Can another engineer understand this without the author explaining it?
- Are names descriptive and consistent with project conventions?
- Is control flow straightforward — no deep nesting or nested ternaries?
- **Could this be done in fewer lines?** 1000 lines where 100 suffice is a failure.
- **Are abstractions earning their complexity?** Don't generalize until the third use case.
- Dead code artifacts — no-op variables, compat shims, `// removed` comments?
- **Is a new conditional bolted onto an unrelated flow?** That is a design smell, not a
  nit. Push the logic into its own helper, state, or policy.
- **Repeated conditionals on the same shape?** They signal a missing model or dispatcher.
  A "temporary" branch is usually permanent.

### 3. Architecture

- Does it follow existing patterns, or introduce a new one? If new, is it justified?
- Are module boundaries clean? Any circular dependencies?
- Is the abstraction level appropriate — not over-engineered, not too coupled?
- **Does this refactor reduce complexity or just relocate it?** Count the concepts a reader
  must hold. If a "cleaner" version leaves that count unchanged, it is not cleaner. Prefer
  restructurings that make whole branches or layers disappear. Prefer deleting an
  abstraction to polishing it.
- **Is feature-specific logic leaking into a shared module?** Keep logic in its owning
  layer; reuse the canonical helper instead of a near-duplicate. If the change touches a
  `frontis`-consumed field, confirm downstream repos were actually checked —
  `pilotariak-context` has the repo map.
- **Are type boundaries explicit?** Question gratuitous `any`/`unknown`/casts and silent
  fallbacks that paper over an unclear invariant.

### 4. Security

- Is user input validated and sanitized at system boundaries?
- Are secrets kept out of code, logs, and version control?
- Are authn/authz checks present where needed?
- Are queries parameterized and outputs encoded?
- Are file paths validated against traversal?
- Is external data — APIs, logs, user content, config — treated as untrusted?
- Any new dependency with known vulnerabilities?

### 5. Performance

- N+1 query or API call patterns?
- Unbounded loops or unconstrained fetching?
- Missing pagination on list operations?
- Unnecessary allocations in hot paths?
- Are contexts used for cancellation and timeouts?

---

## Severity

**This skill is the sole owner of the severity vocabulary.** Do not restate these labels in
a role template or a project skill — reference this section instead.

| Label                       | Blocks merge? | Use for                                                                          |
| --------------------------- | ------------- | -------------------------------------------------------------------------------- |
| **Critical**                | Yes           | Security vulnerability, data loss, broken functionality                          |
| **Required**                | Yes           | Must be fixed before merge — missing test, wrong abstraction, bad error handling |
| **Nit**                     | No            | Formatting, naming, style preference                                             |
| **Optional** / **Consider** | No            | Worth doing; not required for merge                                              |
| **FYI**                     | No            | Informational; no action needed                                                  |

### Non-blocking is about merge-gating, not importance

The "Blocks merge" column says only whether a finding stops *this* merge. It says nothing
about whether the finding can be dropped.

**What happens to a finding after you file it is owned by `software-engineering-process`
→ The review cycle.** Read it there.

Two consequences for how you label:

- Do not reach for **Nit** or **Optional** to make a real finding easier to ignore. If it
  needs to happen, label it **Required**.
- If you genuinely mean "no action needed," the label is **FYI** — not Nit.

### Lead with what matters

Order findings by leverage: correctness and security first, then structural regressions and
missed simplifications, then everything else. A few high-conviction comments beat a long
list. **If you have one structural problem and ten nits, the structural problem is the
review.** Do not bury a real issue under cosmetic ones.

### Propose the move, not just the problem

A review that says "this is complex" leaves the author guessing. When you flag a structural
problem, name the restructuring:

- Replace a chain of conditionals with a typed model or explicit dispatcher.
- Collapse duplicate branches into a single flow.
- Separate orchestration from business logic.
- Move feature-specific logic out of a shared module into the package that owns it.
- Reuse the canonical helper instead of a bespoke near-duplicate.
- Make a type boundary explicit so downstream branching disappears.
- Delete a pass-through wrapper that adds indirection without clarifying the API.
- Extract a helper, or split a large file into focused modules.

Prefer the remedy that removes moving pieces over one that spreads the same complexity
around.

Every **Critical** and **Required** finding must include a specific fix recommendation.

---

## Review Process

1. **Understand the intent.** What is the change trying to accomplish? Read the spec, brief,
   or issue before the code.
2. **Review the tests first.** They reveal intent and coverage. Do they test behavior rather
   than implementation? Are edge cases covered? Would they catch a regression?
3. **Review the implementation** against the five axes, file by file.
4. **Categorize every finding** using the severity table above.
5. **Verify the verification.** What tests were run? Did the build pass? Was it manually
   checked? Is there a before/after for behavioral changes?

Run the repo's own build, lint, and test commands — read its `AGENTS.md` and `make help`
rather than assuming a command exists. `pilotariak-context`'s `references/proving-a-change.md`
has the per-repo table.

**When that gate cannot be run, disclose it — do not skip it silently.** You will often be
reviewing in a fresh container against a sparse or partial checkout: dependencies not
installed, the toolchain absent, or only part of the tree fetched. That is a fact about your
environment, not a finding about the change, and **it is not grounds to withhold a verdict**.
Work down this ladder and stop at the first rung that runs:

- The full build, lint and test commands. This is still the default whenever it is available.
- The narrowest real gate you can reach — one package's tests, lint or format check on the
  changed files only, a type-check, a compile of the touched package.
- A syntax or parse pass over each changed file, or executing a single changed file directly
  where the language allows it.
- Reading the diff against the surrounding code: confirm signatures and call sites the new
  code depends on, per **Verify dependencies but don't review them** below.

Then **say in the verdict which gates you ran and which you could not, and why** — see
**Output Format**. An unrun check must be visible to the reader of the review, not inferable
only from its absence.

### Scope discipline

- **Ignore technical debt outside the diff.**
- **Verify dependencies but don't review them.** If new code calls an existing function,
  verify the signature in the base branch to confirm the call is valid — do not review that
  function.
- **A change too large to review properly is itself a finding.** Ask for a split rather than
  skimming it. Authoring thresholds are owned by `software-engineering-process` → **Sizing**.
- **Tone.** Objective, technically precise, with "Suggested Fix" blocks where they help.

### Dead code hygiene

After reviewing a refactor, check for orphaned code — now-unreachable functions, replaced
components, unused constants. List them explicitly and **ask before deleting**. Do not leave
dead code; do not silently delete something you are unsure about.

---

## Output Format

Report the review by `scion message` to whoever created you — `coordinator` or
`eng-manager` — rather than
writing it to a path only you can see; see `artifact-durability` for why. For a review long
enough that it doesn't fit comfortably in a message body, write it to the project's shared
scratchpad and name that file in your message. Use this structure either way:

```markdown
# <branch/commit identifier>: <title> — Review

## Executive Summary
<2 sentences. State the risk level: LOW / MEDIUM / HIGH / CRITICAL.>

## Critical
<Blocking bugs or security flaws. If none: "None.">

## Required
<Must be fixed before merge. If none: "None.">

## Nit / Optional
<Non-blocking for this merge. Disposition is still owed — see Severity above.>

## FYI
<Informational only.>

## Positive Feedback
<Specific well-implemented logic, if present. If none, say "None." Do not manufacture
praise.>

## Test Coverage
<Are new code paths covered? Gaps?>

## Backward Compatibility
<Wire-format changes? Removed fields? New required fields without a default — and, for a
`frontis` GraphQL field, has a downstream consumer been checked?>

## Final Verdict
APPROVE | REQUEST CHANGES
<Gates run, and any gate you could not run here, with the reason.>
```

**Separate verdict from recommendations.** If you approve with recommendations, forward
them for a cleanup pass. Return a clean APPROVE with no further action only when the change
is genuinely clean.

Do not approve a change with unresolved **Critical** or **Required** findings. Both are
declared blocking in the severity table; APPROVE is available only when what remains is
**Nit**, **Optional**, or **FYI**.

---

## Common Rationalizations

| Rationalization                           | Reality                                                                                                                                                                    |
| ----------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| "It works, that's good enough"            | Working code that is unreadable, insecure, or architecturally wrong compounds into debt.                                                                                   |
| "AI-generated code is probably fine"      | AI code needs *more* scrutiny, not less. It is confident and plausible even when wrong.                                                                                    |
| "The tests pass, so it's good"            | Tests do not catch architecture problems, security issues, or readability.                                                                                                 |
| "The refactor makes it cleaner"           | Relocating complexity is not reducing it. If the reader holds the same number of concepts, the structure did not improve.                                                  |
| "It's only a small addition to this file" | Small diffs still bolt branches onto unrelated flows. Judge the resulting structure, not the diff size.                                                                    |
| "I'll clean it up later"                  | Later rarely comes. Require cleanup before merge, or a filed and assigned follow-up.                                                                                       |
| "It's non-blocking, so they can skip it"  | Non-blocking means it will not block *this merge review*. Disposition is owned by `software-engineering-process` → **The review cycle** — go read it rather than guessing. |

## Red Flags

- "LGTM" with no evidence of actual review.
- A review that only checks whether tests pass.
- Findings with no severity label — the author cannot tell what is required.
- A long list of nits with a structural problem buried inside it.
- Security-sensitive changes reviewed without a security pass — for fabrika, that's
  `security-auditor`'s job on the Chunky/Large tree; name the gap if it's missing on the
  Small-tier path.
- A bug fix with no regression test.
- A refactor that moves code without reducing the concepts a reader must hold.
- New conditionals scattered into unrelated code paths.
- A bespoke helper duplicating an existing canonical one.

---

## Getting the Diff to Review

fabrika review happens **before anything is pushed**: `eng-manager`/`coordinator` start you in a worktree
that shares `.git` with the producer's, pointed at the exact branch the producer named in
its handoff message. There is usually no open PR yet, so `gh pr diff` has nothing to read —
work from the branch directly.

### Compute the merge-base explicitly

Don't trust `A..B` to mean "what changed on this branch" — on a stale branch it will include
upstream commits since divergence. Compute the merge-base against the branch you were
actually pointed at (`main`, or whatever base the producer branched from):

```bash
BASE=$(git merge-base main <producer-branch>)
git log --oneline "$BASE..<producer-branch>"
git diff "$BASE...<producer-branch>"
```

Dot-syntax reminder:

- `git diff A...B` — changes on B's side since divergence (the change-shaped diff).
- `git log A..B` — commits reachable from B but not A (the change's commit list).

### Cap potentially-huge `git` output

Long-lived branches can produce 100KB+ of `git log` output. Always bound it:

```bash
git log --oneline --max-count=30 "$BASE..<producer-branch>"
git log --oneline "$BASE..<producer-branch>" -- some/path/   # scope by path
```

### Suspect "empty output" before re-running

If `git log <range>` returns zero lines, the first hypothesis should be "the revision spec
doesn't resolve the way I think," not "there's nothing to show":

```bash
git rev-parse main 2>&1
git rev-parse <producer-branch> 2>&1
```

### If a PR already exists

Some reviews do happen against an already-pushed PR (a re-review after the person opened
one). In that case `gh` is the better tool — it computes the merge-base server-side and
returns only the PR's hunks regardless of local-ref state:

```bash
gh pr diff <num>
gh pr view <num> --json files,additions,deletions,baseRefName,headRefName,body,author,state
```

---

## Related material

| For                                                      | See                                                               |
| -------------------------------------------------------- | ----------------------------------------------------------------- |
| Review cycle, reviewer rotation, per-PR scope, round cap | `software-engineering-process` → **The review cycle** (this pack) |
| What the author owes on a finding after it is filed      | `software-engineering-process` → **The review cycle** (this pack) |
| Change/project sizing thresholds                         | `software-engineering-process` → **Sizing** (this pack)           |
| How a review report survives the agent that wrote it     | `artifact-durability` (this pack)                                 |
| Deeper, dedicated security review                        | `security-auditor` template (this pack)                           |
| Dedicated load/latency profiling                         | `perf-test` template (this pack)                                  |
