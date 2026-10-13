# Tells of a wrong claim

Moments you're about to overstate something, specific to this org's repos, and
the cheapest check for each.

**"The commit is fine."**
Check it carries `Signed-off-by` and a valid conventional-commit subject before
you say a change is ready for PR — `git log -1 --format='%B'`. A commit missing
either is not fine; the `conventional-pre-commit` hook should have caught it,
but hooks can be skipped or not installed on a fresh clone.

**"It's a Cloudflare Worker repo, so it's `bun`."**
`xilo` uses `npm`. `azkena` and `plaza` are Workers too, but check each repo's
own lockfile (`bun.lock` vs `package-lock.json`) before assuming — don't
pattern-match from a sibling repo's stack.

**"I changed the color/typography token, done."**
In `frontis`, `kancha`, and `website`, `DESIGN.md` is the source of truth and
`src/constants/colors.ts` (or that repo's equivalent) is *generated* from it.
Editing the generated file instead of `DESIGN.md`, or editing `DESIGN.md`
without running `tokens-export`, both leave the repo in a state that
`make tokens-check` — and the `design-lint.yml` CI job — will fail on. Run
`tokens-check` yourself before claiming the change is complete.

**"The GraphQL query works locally, so the schema change is safe."**
Local success doesn't prove the Hive registry or a downstream consumer agrees.
Run `make hive-check` in `frontis` before publishing, and separately check
whether `kancha`, `azkena`, `xilo`, or `plaza` query the field you changed —
a passing local test in `frontis` says nothing about their build.

**"I searched for an existing helper/tool and found nothing, so it doesn't
exist."**
Search by behavior (what it does), not just by the name you'd have picked —
across repos the same concept can be named differently (`hive-check` here,
"schema validation" in your head). Scope the absence claim to what you actually
searched, and say so.

**"CI will catch it anyway."**
`pre-commit` hooks (hawkeye, dprint/formatting, gitleaks, Renovate/Actions
schema checks, conventional-commit) run the same checks locally that CI runs
remotely, just earlier and faster to iterate on. Relying on CI alone to catch
a formatting or header problem costs a round trip for something `pre-commit
run --all-files` would have caught in seconds.
