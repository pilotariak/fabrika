# Link a project to a Scion Hub

Use this when you want agent operations for a git repo routed through a
running Scion Hub instead of staying local — centralized coordination,
state sync, and management from the Hub UI/API.

## 1. Initialize the project

From the repo root:

```bash
scion init
```

Creates the `.scion` directory structure and seeds the default template.
Skip this if the repo is already a scion project.

## 2. Point scion at your Hub

```bash
scion config set hub.endpoint http://localhost:8080
```

Writes `hub.endpoint` into `.scion/settings.json`. You can override it
per-invocation with `--hub <url>` or the `SCION_HUB_ENDPOINT` env var
without touching this setting.

## 3. Enable Hub integration

```bash
scion hub enable
```

Routes agent operations (create, start, delete) through the Hub API
instead of performing them locally. Requires the endpoint from step 2 to
already be set — it errors if none is configured.

## 4. Link the project

```bash
scion hub link
```

Associates this local project with the Hub, creating it on the Hub if it
doesn't exist yet, or linking to an existing one by matching name or git
remote.

## Verify

```bash
scion hub status
```

Confirms the endpoint, enabled state, and link.

To undo: `scion hub disable` (stop routing through the Hub) or
`scion hub unlink` (remove the project association).
