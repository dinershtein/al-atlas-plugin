# AL Atlas Codex plugin

Read-only research access to the collected mainland Portugal RNAL registry.

## Install in Codex

```bash
codex plugin marketplace add https://github.com/dinershtein/al-atlas-plugin
codex plugin add al-atlas@al-atlas
```

Then open a new Codex chat and enable **AL Atlas**. The plugin connects to the
remote MCP server at `https://dev.dinershtein.com/rnal-mcp/mcp`.

No login, OAuth flow or access key is required. The endpoint is public and read-only;
its SQL tool is bounded to prepared relations.

## What it can do

Search RNAL registrations and corporate holders, inspect company portfolios,
aggregate the registry by geography and modality, read source-linked profiles,
and run bounded read-only `SELECT`/`WITH` queries against the prepared `rnal_mcp`
relations. The SQL tool is limited to 100 rows and a five-second timeout.

The plugin contains no credentials. RNAL is a dated mainland Portugal snapshot;
registrations are not audited active inventory, revenue, occupancy or proof of
asset-light operations. See [the plugin guide](plugins/al-atlas/README.md),
[the schema](plugins/al-atlas/docs/DATABASE.md) and [request examples](plugins/al-atlas/docs/EXAMPLES.md).
