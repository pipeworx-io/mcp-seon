# mcp-seon

SEON MCP — wraps the SEON fraud / digital-footprint enrichment APIs (seon.io)

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1576+ live data sources.

## Tools

| Tool | Description |
|------|-------------|
| `seon_email` | Look up the digital footprint of an email address via SEON: deliverability, email provider/domain details, data-breach exposure, and which online/social platforms the address is registered on. Example: seon_email({ email: "john@example.com", _apiKey: "your-seon-license-key" }) |
| `seon_phone` | Look up the digital footprint of a phone number via SEON: carrier and line type, validity, and which online/social/account platforms the number is linked to. Example: seon_phone({ phone: "+13106667777", _apiKey: "your-seon-license-key" }) |
| `seon_ip` | IP intelligence via SEON: VPN / web-proxy / public-proxy / data-center-proxy / residential-proxy / Tor flags plus geolocation (country, city, lat/long). Example: seon_ip({ ip: "8.8.8.8", _apiKey: "your-seon-license-key" }) |
| `seon_fraud` | Aggregate fraud assessment via SEON: combine email, phone, and/or IP into a single fraud_score with the applied_rules that fired and a decision state (e.g. APPROVE/REVIEW/DECLINE). Pass at least one of email/phone/ip. Example: seon_fraud({ email: "john@example.com", ip: "8.8.8.8", _apiKey: "your-seon-license-key" }) |

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "seon": {
      "url": "https://gateway.pipeworx.io/seon/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/seon/mcp` returns the tools in the table
above **plus the shared Pipeworx meta-tools** — `ask_pipeworx`,
`discover_tools`, `search_within`, `remember`/`recall` and the rest of the
gateway-wide set. So the tool count you see is larger than this table: a
single-pack endpoint currently lists roughly 30 shared tools alongside the
pack's own. The connection's `initialize` response states its exact scope, and
is the authoritative answer for a given day.

This is deliberate, not multiplexing by accident. The meta-tools are what let a
scoped connection answer a question this pack does not cover — via
`ask_pipeworx`, which routes across the whole catalog — without you adding a
second MCP server. There is currently no way to mount a pack endpoint without
them; if the extra schemas cost you more context than the routing is worth,
connect to the full gateway once rather than to several pack endpoints.

Or connect to the full Pipeworx gateway to get every pack's tools listed
directly, instead of just this one's:

```json
{
  "mcpServers": {
    "pipeworx": {
      "url": "https://gateway.pipeworx.io/mcp"
    }
  }
}
```

Both URLs reach the same gateway and the same 1576+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

This pack takes your own API key (`_apiKey`) — we don't front one for it, so there's no curl here that would run without it. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/seon_email`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "seon": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-seon"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-seon
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Seon data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
