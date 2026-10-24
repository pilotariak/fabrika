# Sync templates to a Hub instead of resolving by `gh://`

Use this if `gh://` latency on every `scion start` becomes a problem, or you
want to reference templates by bare name instead of the full URI.

```bash
cd fabrika && scion templates sync --all
# elsewhere: scion start coordinator "<your goal>" --type coordinator
```

A synced copy needs a re-sync (`scion templates sync --all`) after every edit
here. Resolving a template straight by `gh://` URI is expected to pick up
edits on the next start without a sync step, but that path is also the least
exercised part of this setup — see
[unresolved, on purpose](../explanation/why-scion.md#unresolved-on-purpose).
