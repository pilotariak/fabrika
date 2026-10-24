# Add a new role template

Use this when a recurring task shape genuinely doesn't fit `lead`, `builder`,
`code-reviewer`, `qa`, or the fourteen-role `pm`/`po`/`db`/`tpm`/… tree — not
preemptively. See
[why this setup stops at four roles at the Small tier](../explanation/why-scion.md#why-only-four-roles-at-the-small-tier)
and [the 14-role tree's own honest provenance](../explanation/why-scion.md#the-14-role-tree)
before adding a fifteenth; the bar is "this keeps happening and none of the
existing roles own it," not "this role exists in some other Scion template
library."

## 1. Create the directory

```bash
mkdir -p .scion/templates/<role-name>
```

The directory name **is** the template name — there's no separate `name`
field to keep in sync.

## 2. Write the three files

Every existing fabrika template follows this shape; match it:

- **`scion-agent.yaml`** — the only file Scion actually parses. Needs
  `schema_version: "1"`, a one-line `description`, `agent_instructions:
  agents.md`, `system_prompt: system-prompt.md`, and a `default_harness_config`
  (`codex` or `claude` — pick based on which harness should own this role's
  judgment calls, the way `code-reviewer` deliberately runs on a different
  harness than `builder`).
- **`system-prompt.md`** — 3-5 lines. Identity and the one-sentence job, not
  the operational detail.
- **`agents.md`** — the operational instructions: what to do, in what order,
  what to hand off and to whom. This is where the actual behavior lives.

## 3. Decide what skills it needs

Start with:

```yaml
skills:
  - uri: "gh://Pilotariak/fabrika/pilotariak-context"
```

every role needs the org facts. Beyond that, check
[scion-frontiers/agent-team](https://github.com/scion-frontiers/agent-team/tree/main/skills)
for something that already solves the problem — `software-engineering-process`
for anything that creates and supervises other agents,
`agent-state-continuity` for anything that needs to survive a container
restart — before writing a new one. If you do add an external skill,
**write a comment above the `uri:` line saying why**, the way
`code-reviewer/scion-agent.yaml` does for
`software-engineering-process` — the next person reading the file shouldn't
have to reconstruct the reasoning.

Keep the `pilotariak-context` skill itself narrow: org facts no single
repo's own `AGENTS.md` states. A new role's domain-specific knowledge
belongs in that role's own `agents.md`, not folded into the shared skill.

## 4. Decide who creates it, and who it reports to

This is the part that's easy to get wrong — see
[why the builder doesn't spawn its own reviewer](../explanation/why-scion.md#delegation-is-agent-initiated).
Figure out, before writing `agents.md`:

- Who creates this role (`scion start ... --type gh://Pilotariak/fabrika/.scion/templates/<role-name>`)?
  That agent is the one subscribed to its notifications.
- Does it report back by message, or is it a terminal step in the chain?
- If it reviews another agent's work, confirm it's never the same agent that
  produced that work — the one invariant `software-engineering-process`
  doesn't let you collapse away.

## 5. Update the reference docs

Add a row to the "Templates" table in
[`docs/reference/README.md`](../reference/README.md).

## 6. Validate before committing

```bash
python3 -c "import yaml,sys; yaml.safe_load(open(sys.argv[1]))" .scion/templates/<role-name>/scion-agent.yaml
uvx pre-commit run --all-files
```

The YAML check catches syntax errors; pre-commit catches formatting
(`rumdl` on the markdown tables, `yamlfmt` on indentation) and the license
header on the yaml file.

## 7. Prove it resolves

`gh://` templates only resolve once committed and pushed — there's no local
dry-run for the URI form. Test from a real target repo once this has landed
on a branch the Hub can fetch:

```bash
scion start test-<role-name> "a trivial task" \
  --type gh://Pilotariak/fabrika/.scion/templates/<role-name>
```

If it needs to be tested before merging to `main`, use the local path form
instead: `--type /path/to/your/fabrika/checkout/.scion/templates/<role-name>`.
