# Contributing with agents

The loop is the same in every Pilotariak repo. What differs between repos is
only the commands in step 4 — see [proving a change](../craft/proving-a-change.md).

## 1. Start from the repo's own map

Read that repo's `AGENTS.md` first (every repo except `decisions`,
`infrastructure` and `plaza` has one as of this writing). It names the stack,
the directory layout, and the dev-setup commands. Don't assume a pattern from
one repo (e.g. `bun`) holds in another — `xilo` uses `npm`, not `bun`, even
though it looks identical to `azkena` at a glance.

## 2. Find the owner of what you're changing

`frontis` is the hub (see [what Pilotariak is for](../identity/what-pilotariak-is-for.md)).
Before changing a GraphQL field, a shared type, or anything under `frontis/`,
check whether `kancha`, `azkena`, `xilo` or `plaza` query it — a breaking schema
change is a breaking change in every downstream repo at once, silently, until
someone runs them.

## 3. Work in a branch, commit with sign-off

```bash
git commit -s -m "fix(gateway): handle null specialty in results query"
```

- `-s` adds the DCO `Signed-off-by` trailer. Required, not optional — see
  [CONTRIBUTING.md](../CONTRIBUTING.md) in this repo for the full text, and the
  equivalent file in the repo you're changing.
- The subject must be a valid conventional-commit type/scope/subject. The
  `conventional-pre-commit` hook rejects anything else before it enters history.
- Never add a `Co-authored-by` trailer (org-wide policy) — DCO sign-off is the
  attribution mechanism here, not co-authorship.

## 4. Run the hooks, then the repo's own checks

`pre-commit` (hawkeye, formatting, gitleaks, config schema checks) runs on every
commit if installed; run `pre-commit run --all-files` once after cloning to
catch what a partial run would miss. Then run what `make help` in that specific
repo lists — typically `lint`, `test`, and `typecheck`/`types`. Full command
tables are in [proving a change](../craft/proving-a-change.md).

## 5. Open the pull request

- CODEOWNERS auto-requests review; there is currently one owner across the org,
  so "review" here means a second, independent pass — by another agent or by
  re-reading your own diff after a break — not a different human approving it.
  Say what you checked and what you didn't, rather than asserting the change is
  correct.
- `release-please` reads the merged commit history to propose the next release;
  you don't tag releases by hand.

## After a context reset

If your context was compacted, cleared, or you're a fresh agent picked up
mid-task: don't trust a stale summary of branch state. Re-derive it:

```bash
git status
git log --oneline -5
git diff --stat main...HEAD
```

Then re-open the issue or PR you were working from and re-read its current
state — not your memory of it.
