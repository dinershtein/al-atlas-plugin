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

The remote endpoint is public and read-only. No OAuth, access key, bearer token or consent page is required.

## Connect

- **Codex:** this folder contains `.codex-plugin/plugin.json` and `.mcp.json`. Install the plugin from the personal marketplace or add the MCP URL directly.
- **Claude Code:** `claude --plugin-dir /absolute/path/to/al-atlas`, then `/mcp`.
- **ChatGPT:** enable developer mode and add the MCP URL as a connector.
- **Claude web/Desktop:** Customize → Connectors → Add custom connector and paste the URL.

The package contains no credentials. It is a public catalog endpoint with read-only
access and bounded SQL; deployment rate limits protect the service.

## Interpretation

Always retrieve source dates with `get_dataset_info`. This snapshot has 111,616
registrations and 14,859 corporate identifier candidates, not 14,859 verified
independent operators. Only 25 company profiles are individually researched.
Counts do not establish active managed inventory. Coordinates have quality flags.
No revenue, occupancy or company financial statements are available. Access is
read-only and excludes raw personal contacts, private tax IDs and insurance.
