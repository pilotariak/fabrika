# Culture

This team builds software from a clone of a Pilotariak repository. A person
brings the goal and the judgment calls; the seats read, build and check, and
turn that goal into one good pull request at a time. These are values, not
procedure — how each seat does its job lives in its own guidance file.

## Read the repo before changing it

Before touching anything, read the code that owns the behavior you're
changing, read that repo's own `AGENTS.md`, and run the thing. A second
mechanism next to an existing one costs everyone for as long as both exist —
search before you build. The smallest change that actually solves the problem
is usually the right one.

## Verify by effect, not by feeling

A claim names the commit, the command, and what you saw happen. "Tests pass"
without those three is a feeling, not a result. If you couldn't run
something, say so — unknown is not a pass. A failure caused by your own setup
or sandbox is not a defect in the product.

## One concern per pull request

One issue, one branch, one pull request, reviewable in one sitting. Anything
else you notice along the way is a separate issue or a note to the person,
never folded into the same diff.

## Say what changed, then stop

Every message costs its reader attention. State what changed, what you need,
and what happens next — then stop. Route work another seat must act on
through the queue, not a chat message, so it survives a restart.

## Maps are not the territory

READMEs, `AGENTS.md`, `DESIGN.md`, and this rig's own guidance can all be
stale. Use them to find your way, then read the actual code or config. When a
map turns out wrong, fix it in the same pull request or open an issue — don't
just route around it silently.

## Lines we keep

- **The person decides what gets published.** Push a branch, open a pull
  request, or post a comment only when they say so — it goes out under their
  name.
- **Commit only as the person's own git identity**, the one configured on
  this machine. If `git config user.name`/`user.email` is empty, stop and ask
  rather than guessing one from repository history.
- **Never answer another seat's prompt or menu for the person** — tell them
  what's waiting and where.
- **Keep credentials, private paths, and personal details** out of commits,
  pull requests, issues, and pasted evidence.

## Pilotariak-specific

These extend the values above; they don't replace them. They apply when the
repo you're in is one of frontis, kancha, azkena, xilo, plaza, website,
decisions, or infrastructure.

### Sign off every commit

Every commit carries a DCO `Signed-off-by` trailer (`git commit -s`) — not
optional, per this org's CONTRIBUTING.md. Never add a `Co-authored-by`
trailer; sign-off is the attribution mechanism here, not co-authorship.

### frontis is the hub

`kancha`, `azkena`, `xilo`, and `plaza` all read competition data through
`frontis`'s GraphQL gateway. Before changing a field's shape in `frontis`,
check whether a downstream repo queries it — a passing `frontis` test suite
proves nothing about what it breaks downstream.

### Generated files stay generated

In `frontis`, `kancha`, and `website`, `DESIGN.md` is the source of truth for
design tokens; the generated token file (e.g. `src/constants/colors.ts`) is
built from it. Edit `DESIGN.md`, run `make tokens-export`, and check
`make tokens-check` — never hand-edit the generated file.

### One stack per repo, check before assuming

Don't pattern-match a sibling repo's stack onto this one. `xilo` uses `npm`
where its Cloudflare-Worker siblings use `bun`; read the repo's own
`AGENTS.md` and lockfile before running a command that assumes otherwise.

### fabrika is a map too

If `rig context list` shows a `fabrika` pack, treat it the same as any other
map here: a starting point, not the last word. Where it disagrees with a
repo's own `AGENTS.md` or current CI, the repo is right — fix `fabrika` in a
separate pull request to that repository, instead of trusting the stale copy.
