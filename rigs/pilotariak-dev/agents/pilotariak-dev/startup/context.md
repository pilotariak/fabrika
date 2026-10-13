# Start (every seat on this rig)

1. Run `rig whoami --json`. It names your seat and your peers' sessions. Use
   those names; don't guess addresses. During launch a peer may not be listed
   yet — if one of the four seats is missing, check `rig ps --nodes` again
   shortly before telling anyone it's missing.
2. Find the repository you work in: `git rev-parse --show-toplevel`. If that
   fails, or your working directory is this rig's own folder, the rig was
   launched without `--cwd` — stop and say so.
3. Read that repo's own maps before anyone changes anything: its `AGENTS.md`
   if it has one, its `README`, and `make help` for the commands it exposes.
4. If `rig context list` shows a `fabrika` pack, load it:
   `rig context profile fabrika --situation fresh`. It covers the Pilotariak
   repo map, the shared contribution loop, the frontis-is-the-hub caution,
   and per-repo commands that prove a change works — read it in addition to
   step 3's repo-local maps, not instead of them.

Last, submit your startup proof: run the `rig startup-proof submit` command
from the "OpenRig startup orientation challenge" in your startup text, exactly
as given.

Then the lead starts from the person's goal and follows its own "first move"
guidance. Everyone else waits for work from the lead — an idle seat is fine.
