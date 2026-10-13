# Agentic automation: the frontis pattern

`frontis` runs several CI workflows that are themselves agents, built with
[GitHub Agentic Workflows](https://github.com/githubnext/gh-aw) (`gh aw`): a
natural-language workflow (`.github/workflows/aw-*.md`, frontmatter + prompt)
compiles to a locked, reviewable `.lock.yml`. As of this writing, no other
Pilotariak repo has adopted this — `frontis` is the pilot, not an org-wide
standard yet.

## What frontis runs today

| Workflow                      | Trigger              | Does                                                                           |
| ----------------------------- | -------------------- | ------------------------------------------------------------------------------ |
| `aw-issue-triage-agent`       | Scheduled (weekdays) | Labels untriaged issues (`kind/*`, `status/available`), removes `needs/triage` |
| `aw-pr-triage-agent`          | PR events            | Equivalent triage pass for pull requests                                       |
| `aw-pr-stale-agent`           | Scheduled            | Flags/closes stale PRs                                                         |
| `aw-dependabot-issue-bundler` | Scheduled            | Groups related Dependabot issues                                               |
| `aw-dependabot-pr-bundler`    | Scheduled            | Groups related Dependabot PRs                                                  |
| `repo-assist`                 | —                    | General repo-assist agent                                                      |

Each has `safe-outputs` scoping exactly what it's allowed to do (e.g.
`add-labels` with an explicit allow-list, `add-comment`) and runs GitHub's
`lockdown: true` tool mode when it reads untrusted content (issue bodies),
which blocks prompt injection from reaching write-capable tools. That scoping
is the part worth copying deliberately, not the workflows themselves — don't
port a `.lock.yml` file verbatim into another repo without rereading what
permissions and safe-outputs it grants there.

## Extending it to another repo

If you're asked to add this pattern to `kancha`, `azkena`, `xilo`, `plaza`, or
`website`:

1. Check whether `gh aw` is already a dev dependency or needs installing in
   that repo (`frontis`'s `package.json`/`Makefile` is the reference).
2. Start from the `.md` source, not the compiled `.lock.yml` — the frontmatter
   (`tools`, `safe-outputs`, `permissions`, `lockdown`) is the part you read
   and adapt; the lock file is generated output.
3. Scope `safe-outputs` to the smallest set of labels/actions that repo
   actually uses — don't copy `frontis`'s `kind/*` label set if the target
   repo's labels differ.
4. Keep `lockdown: true` wherever the prompt includes content an outside
   contributor wrote (issue/PR title, body, comments). That's the control that
   makes "an agent reads untrusted GitHub content" safe to automate.
5. This is still a pilot pattern, not a requirement — don't add it to a repo
   because this file exists; add it because that repo has the same
   triage/staleness problem `frontis` had.
