# Import templates into a Hub

Use this to pull Scion templates from a repo (fabrika or any other) into
your local cache and publish them to a running Hub so others can resolve
them by bare name.

`scion templates import` and `scion templates sync` are two different
steps — import never talks to the Hub, no matter what flags you pass it.

**Scope matters.** Scion has two independent local caches, and the Hub
keeps them separate too:

- **project** scope (default) — `.scion/templates/` in the repo, synced
  under that project's page on the Hub
  (`<hub>/projects/<project-id>/templates`).
- **global** scope (`--global` / `scion --global ...`) — `~/.scion/templates/`,
  synced under the Hub's built-in `Global` project and listed at
  `<hub>/settings`.

If you only sync project scope, `<hub>/settings` will correctly say
"No global templates yet" — that's not a bug, it's a different list.
Repeat steps 1 and 3 with `--global` if you want the templates to show up
there too.

## 1. Import from the source repo

```bash
scion templates import https://github.com/pilotariak/fabrika/tree/main/.scion/templates --all
```

This writes templates to your **local** project cache
(`.scion/templates/<name>/`) only. `--hub` and `--force` do nothing to
change that — `--hub` is a global flag other hub-aware commands read, but
`import` ignores it; `--force` only controls overwriting local files that
already exist.

Verify what landed locally:

```bash
scion templates list
```

## 2. Make sure Hub integration is enabled

```bash
scion hub status
```

If `Enabled: false`, `sync` will refuse with
`hub integration is not enabled, use 'scion hub enable' first`. Fix it:

```bash
scion hub enable
```

First run against a given Hub also prompts to link the project — accept it
(`Y`), or it creates/links one automatically in `--yes`/non-interactive
mode. This step is one-time per project/Hub pair.

## 3. Sync the imported templates to the Hub

```bash
scion templates sync --all
```

This is the step that actually uploads — each template's files go up and
the Hub returns an ID, status, and content hash per template. Without it,
step 1's templates stay purely local and never reach the Hub UI.

## Verify

```bash
scion templates list --hub http://127.0.0.1:8080
```

Open the project's template page on the Hub
(`<hub>/projects/<project-id>/templates` — `scion hub status` prints the
project ID) to see project-scope templates. `<hub>/settings` only ever
shows global-scope ones (see "Scope matters" above).

## Optional: also push to global scope

Repeat the same two commands with `--global` if you want these templates
to resolve for every project on this machine and show up at
`<hub>/settings`:

```bash
scion --global templates import https://github.com/pilotariak/fabrika/tree/main/.scion/templates --all
scion --global hub enable   # one-time, separate from project-scope enable
scion --global templates sync --all
```

A later edit to a local template needs a re-`sync --all` (project or
global, matching how you imported it) to reach the Hub; sync doesn't watch
for changes.
