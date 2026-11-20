# Dune MCP Server

Dune MCP is Dune's official remote [Model Context Protocol](https://modelcontextprotocol.io/) server. It gives your AI agent direct, structured access to Dune: discover datasets, create and run SQL queries, read execution results, and build visualizations and dashboards, all in the same conversation.

Your agent gets the full Dune data warehouse: raw, decoded and curated onchain data across 100+ blockchains, plus community-built tables.

- **Endpoint:** `https://api.dune.com/mcp/v1`
- **Transport:** Streamable HTTP
- **Auth:** sign in with your Dune account (OAuth 2.0). Use an API key only for headless use.
- **Docs:** [docs.dune.com/docs/agents/mcp](https://docs.dune.com/docs/agents/mcp)

Dune hosts this server. This repository holds the setup guide and the [`server.json`](server.json) metadata for MCP registries. It does not contain server code, and you do not need to install anything.

Works with any MCP-compatible client, including Claude, Cursor, ChatGPT, Codex, and OpenCode.

## Quick start

Most clients only need two values:

| Field | Value |
| ----- | ----- |
| Name  | `Dune` |
| URL   | `https://api.dune.com/mcp/v1` |

When you add the server, your client opens the Dune sign-in page in your browser. Sign in, go back to your client, and it is ready to use. You do not need an API key.

## Setup by client

### Claude Code

```bash
claude mcp add --scope user --transport http dune https://api.dune.com/mcp/v1
```

Then run `/mcp` in Claude Code and select `dune` to sign in.

### Claude Desktop

Add a custom connector with the name `Dune` and the URL `https://api.dune.com/mcp/v1`, then connect it and sign in with your Dune account. See the [click-through demo](https://demo.arcade.software/Qu3RDWVzZXbHQtoN0DVD).

### Cursor

Add this to your Cursor MCP settings, then sign in with your Dune account when Cursor asks:

```json
{
  "mcpServers": {
    "dune": {
      "url": "https://api.dune.com/mcp/v1"
    }
  }
}
```

See the [click-through demo](https://demo.arcade.software/C8YI656Qh2cWRc1Gtrp1).

### Codex

```bash
codex mcp add dune --url "https://api.dune.com/mcp/v1"
codex mcp login dune
```

Codex stops MCP tool calls after 60 seconds by default. A long-running query can then close the connection, and later calls fail with `Transport closed` until you start a new session ([openai/codex#11489](https://github.com/openai/codex/issues/11489)). To prevent this, increase the tool timeout in your Codex config:

```toml
[mcp_servers.dune]
url = "https://api.dune.com/mcp/v1"
tool_timeout_sec = 300
```

### OpenCode

Run `opencode mcp add` and answer the prompts:

| Prompt | Answer |
| ------ | ------ |
| Location | Global |
| MCP server name | `Dune` |
| MCP server type | Remote |
| MCP server URL | `https://api.dune.com/mcp/v1` |
| Does this server require OAuth authentication? | Yes |
| Do you have a pre-registered client ID? | No |

Then sign in:

```bash
opencode mcp auth Dune
```

### ChatGPT and other clients

Add Dune as a custom MCP server (or connector) with the name `Dune` and the URL `https://api.dune.com/mcp/v1`, then sign in with your Dune account when the client asks.

### Headless and CI environments

Use this only when your agent runs where it cannot sign in with a browser, for example in CI. In all other cases, sign in with your Dune account as shown above.

Send your [Dune API key](https://docs.dune.com/api-reference/overview/authentication) in the `x-dune-api-key` header. Do not put the key in the URL.

```bash
claude mcp add --scope user --transport http dune https://api.dune.com/mcp/v1 --header "x-dune-api-key: <dune-api-key>"
```

For clients that use a JSON config:

```json
{
  "mcpServers": {
    "dune": {
      "url": "https://api.dune.com/mcp/v1",
      "headers": {
        "x-dune-api-key": "<dune-api-key>"
      }
    }
  }
}
```

## Tools

| Category | Tool | Description |
| -------- | ---- | ----------- |
| Discovery | `searchDocs` | Search Dune docs for guides, examples, and API references |
| Discovery | `searchTables` | Find tables by protocol, chain, category, or schema |
| Discovery | `listBlockchains` | List indexed blockchains with table counts |
| Discovery | `searchTablesByContractAddress` | Find decoded event/call tables for a contract address |
| Discovery | `getTableSize` | Estimate the data scanned by a query for one or more tables |
| Query Lifecycle | `createDuneQuery` | Create and save a new Dune query |
| Query Lifecycle | `getDuneQuery` | Fetch SQL and metadata for an existing query |
| Query Lifecycle | `updateDuneQuery` | Update SQL, title, description, tags, or parameters |
| Query Lifecycle | `executeQueryById` | Execute a saved query and return an execution ID |
| Query Lifecycle | `getExecutionResults` | Fetch status and results for a query execution |
| Materialized Views | `createMaterializedView` | Materialize a saved query's results into a queryable table, with an optional scheduled refresh |
| Materialized Views | `listMaterializedViews` | List materialized views owned by you or your team |
| Materialized Views | `getMaterializedView` | Fetch a materialized view's metadata, size, and execution status |
| Materialized Views | `refreshMaterializedView` | Re-run the source query on demand to update the materialized table |
| Materialized Views | `updateMaterializedView` | Change the performance tier or add/change/remove the refresh schedule |
| Materialized Views | `deleteMaterializedView` | Permanently delete a materialized view |
| Visualization | `generateVisualization` | Create charts, counters, and tables from query results |
| Visualization | `getVisualization` | Fetch details of an existing visualization |
| Visualization | `updateVisualization` | Update an existing visualization's configuration |
| Visualization | `deleteVisualization` | Delete a visualization |
| Visualization | `listQueryVisualizations` | List all visualizations attached to a query |
| Dashboard | `createDashboard` | Create a new dashboard with widgets and layout |
| Dashboard | `getDashboard` | Fetch details of an existing dashboard |
| Dashboard | `updateDashboard` | Update a dashboard's content, layout, or metadata |
| Dashboard | `archiveDashboard` | Archive a dashboard |
| Folders | `listDuneFolders` | List folders in your library, with item counts |
| Folders | `getDuneFolder` | Fetch a folder with its subfolders, queries, and dashboards |
| Folders | `createDuneFolder` | Create a folder at the top level or inside another folder |
| Folders | `updateDuneFolder` | Rename a folder or change its description, color, or icon |
| Folders | `moveDuneFolder` | Move a folder under another folder or to the top level |
| Folders | `deleteDuneFolder` | Delete a folder, or a folder and everything inside it |
| Folders | `moveContentToFolder` | Move queries and dashboards into a folder or out of folders |
| Schedules | `listSchedules` | List query, dashboard, and materialized view schedules |
| Schedules | `setSchedule` | Run a query or refresh a dashboard on a schedule |
| Schedules | `deleteSchedule` | Stop the schedule of a query or a dashboard |
| Account | `getUsage` | Check current billing-period credit usage |

### Resources

| Resource | Description |
| -------- | ----------- |
| `query-engine-writing-efficient-queries` | Performance and cost best practices for DuneSQL |
| `query-engine-functions-and-operators` | DuneSQL function/operator reference |

The [docs](https://docs.dune.com/docs/agents/mcp#tools-and-resources-inventory) have the current list.

## Example prompts

- "Find the best Dune table for tracking Uniswap v3 swap volume on Ethereum, then create and run a daily volume query for the last 90 days."
- "I have contract `0xa0b86991c6218b36c1d19d4a2e9eb0ce3606eb48`. Find decoded tables, build a weekly transfer count query, execute it, and show the top 20 recipient wallets."
- "Create a dashboard showing Ethereum activity in the past 30 days. Include daily transactions, active addresses, and gas usage."
- "Materialize my daily active addresses query into a table and refresh it every hour, then list my materialized views to confirm."

## Pricing

The MCP server uses your existing [Dune API credits](https://docs.dune.com/api-reference/overview/billing). Check your usage with the `getUsage` tool. See [pricing](https://dune.com/pricing) for plan details.

## Connection details

- Protocol: remote MCP over Streamable HTTP
- `POST https://api.dune.com/mcp/v1` for MCP requests
- `GET https://api.dune.com/mcp/v1` for stream resumption and event replay

OAuth 2.0:

| Item | Value |
| ---- | ----- |
| Authorization server issuer | `https://dune.com/oauth/mcp` |
| Discovery metadata | `https://dune.com/.well-known/oauth-authorization-server/oauth/mcp` |
| Authorization endpoint | `https://dune.com/oauth/mcp/authorize` |
| Token endpoint | `https://dune.com/oauth/mcp/token` |
| Dynamic client registration | `https://dune.com/oauth/mcp/register` |
| JWKS | `https://dune.com/oauth/mcp/jwks.json` |
| Grant types | `authorization_code`, `refresh_token` |
| Client auth at token endpoint | `none` (public clients) |
| PKCE | required (`S256`) |
| Scope | `mcp:dune:full` |
| Resource parameter | `https://api.dune.com` |

## Troubleshooting

- **`Unauthorized` from the MCP endpoint:** sign in again with your Dune account. In a headless environment, check that the `x-dune-api-key` header has a valid key.
- **Codex `Transport closed` during long queries:** increase `tool_timeout_sec` as shown in the [Codex](#codex) section, and start a new session if the connection already closed.
- More fixes, including OAuth errors, are in the [docs](https://docs.dune.com/docs/agents/mcp#troubleshooting).

## Not the docs MCP

`https://docs.dune.com/mcp` is a different MCP server. It only searches the Dune documentation site. To query data and build queries, visualizations, and dashboards, use `https://api.dune.com/mcp/v1`.

## Related

- [Dune MCP docs](https://docs.dune.com/docs/agents/mcp)
- [Dune for AI agents](https://dune.com/agents)
- [Dune CLI and Agent Skills](https://docs.dune.com/docs/agents/cli-and-skills): a terminal alternative to MCP. Skills are in [duneanalytics/skills](https://github.com/duneanalytics/skills).
- [Dune MPP](https://docs.dune.com/docs/agents/mpp): pay per request from your agent with the Machine Payment Protocol.
- Security: Dune is SOC 2 Type II attested. Read the [announcement](https://dune.com/blog/dune-soc-2-type-ii-attestation) or request the report in the [Trust Center](https://trust.dune.com/).

## Support

- Email: [support@dune.com](mailto:support@dune.com)
- Privacy policy: [dune.com/privacy](https://dune.com/privacy)

## License

[MIT](LICENSE)
