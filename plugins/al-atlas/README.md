# AL Atlas

Read-only research over the collected mainland Portugal RNAL registry.

**MCP URL:** `https://dev.dinershtein.com/rnal-mcp/mcp`

## Documentation included in this plugin

- [Database schema](docs/DATABASE.md): all 17 tables, two materialized views,
  seven views, important fields, joins, counting rules and SQL examples.
- [Request examples](docs/EXAMPLES.md): natural-language requests mapped to MCP
  tools and arguments, including company rankings, holder roles, company
  properties, market statistics, maps and bounded SQL analytics.

The server also exposes `rnal://schema`, `rnal://examples`, `rnal://methodology`.
Its initialization instructions tell the assistant to consult them. Tool schemas
describe their parameters, limits and counting semantics. The plugin has eleven
bounded tools, including `run_sql` for one read-only SELECT/WITH query.

Authentication happens on the first tool call. If Codex reports `401 Unauthorized`
before showing a browser page, reconnect the plugin's MCP server and complete the
OAuth page. In Codex CLI the recovery command is `codex mcp login al-atlas`; in the
desktop app use the plugin's **Connect / Reconnect** action. A 401 in a session means
that session has no access token yet; it does not mean the RNAL data is unavailable.

## Connect

- **Codex:** this folder contains `.codex-plugin/plugin.json` and `.mcp.json`.
  On the originating server it is in the personal plugin marketplace as `al-atlas`.
  Install and authorize the MCP connection in the plugin interface.
- **Claude Code:** `claude --plugin-dir /absolute/path/to/al-atlas`, then `/mcp`
  to authenticate. [Official plugin reference](https://code.claude.com/docs/en/plugins-reference).
- **ChatGPT:** enable developer mode, create an MCP connection from
  [Plugins](https://chatgpt.com/plugins) using the URL above and OAuth.
  [Official guide](https://developers.openai.com/plugins/build/plugins).
- **Claude web/Desktop:** Customize → Connectors → + → Add custom connector,
  enter the URL and connect. An organization owner must first add it for the team.
  [Official guide](https://support.claude.com/en/articles/11175166-get-started-with-custom-connectors-using-remote-mcp).

Enter the owner-issued workspace access key on the AL Atlas consent page, never
in a chat. The package contains no credentials. It is a private workspace, not a
public directory listing or multi-customer billing system. The server protocol
and both plugin manifests have been tested; native account installation is a
separate step requiring your ChatGPT/Claude account.

## Interpretation

Always retrieve source dates with `get_dataset_info`. This snapshot has 111,616
registrations and 14,859 corporate identifier candidates, not 14,859 verified
independent operators. Only 25 company profiles are individually researched.
Counts do not establish active managed inventory. Coordinates have quality flags.
No revenue, occupancy or company financial statements are available. Access is
read-only and excludes raw personal contacts, private tax IDs and insurance.
