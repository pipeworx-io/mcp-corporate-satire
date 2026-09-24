# @pipeworx/corporate-satire

Comedy generator: turns a plain announcement into a fabricated, satirical
corporate statement in the shape of a company news bulletin. Everything it
returns is invented for entertainment — the spokesperson quote, the boilerplate,
the media contact, the `stakeholders_aligned` count. For real wire-service
announcements use `globenewswire`, `prnewswire` or `einpresswire`; for company
filings use `edgar`.

Part of [Pipeworx](https://pipeworx.io) — an MCP gateway connecting AI agents to 1679+ live data sources.

## Tools

- `corporate_satire_generate(announcement, company?, tone?, location?)` —
  returns `press_release` (the upstream's field name: title, body, boilerplate,
  media contact as one text block) plus joke metadata: `actual_news_value`,
  `quote_said_by`, `workshops_this_required`, `months_in_planning`,
  `stakeholders_aligned`, `press_pickup_probability`, `original_thing` /
  `new_thing`. `tone` is one of `visionary`, `disrupting`, `humbled`,
  `transparent`, `pivoting`.

## Auth

Keyless. The endpoint answers a bare request (verified 2026-09-21, HTTP 200
with no `X-API-Key`). Earlier versions of this pack shipped a literal key in
source; it was removed in fleet #2287 because the upstream does not need it.

## Data sources

- <https://api.stupidapis.com/press-release/generate> — the generator.
  GET with `announcement`, `company`, `tone`, `location` as query params.
- <https://api.stupidapis.com/> — the catalogue of StupidAPIs endpoints
  (`api_of_the_day`, tool schemas).

## History

Renamed from `press-release` / `press_release_generate` on 2026-09-21 (fleet
#2287). The old name was the #1 `search_packs` hit for "latest press release
from <company>" and handed that query to a joke generator. A first attempt at
`satire-press-release` still ranked #1: `search_packs` scores a slug hit at +15
per query token, so any slug carrying "press" and "release" beats the real wire
packs by construction. Hence a slug with neither word. Both old gateway paths
(`/press-release/mcp`, `/satire-press-release/mcp`) still resolve to this pack
via `RENAMED_PACK_SLUGS` in the gateway, because the npm package and MCP
Registry entry published under the original name point at the first.

## Quick Start

Add to your MCP client (Claude Desktop, Cursor, Windsurf, etc.):

```json
{
  "mcpServers": {
    "corporate-satire": {
      "url": "https://gateway.pipeworx.io/corporate-satire/mcp"
    }
  }
}
```

### What this endpoint actually serves

`tools/list` at `https://gateway.pipeworx.io/corporate-satire/mcp` returns the tools in the table
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

Both URLs reach the same gateway and the same 1679+ data sources. The
only difference is which pack's tools are listed **directly**; `ask_pipeworx`
reaches all of them from either one.

## No MCP client? Call it over HTTP

```bash
curl -X POST https://gateway.pipeworx.io/v1/tools/corporate_satire_generate \
  -H 'Content-Type: application/json' \
  -d '{"announcement":"We are launching a new AI-powered analytics platform that helps businesses make data-driven decisions in real-time.","company":"TechVision Inc.","tone":"disrupting","location":"San Francisco, CA"}'
```

No account needed for the first calls. Inspect any tool: `GET https://gateway.pipeworx.io/v1/tools/corporate_satire_generate`. Find one: `POST https://gateway.pipeworx.io/v1/tools/search_packs` with `{"query":"..."}`.

## Standalone (no gateway account)

This package also runs as a local stdio MCP server — no Pipeworx account, no
gateway round-trip:

```json
{
  "mcpServers": {
    "corporate-satire": {
      "command": "npx",
      "args": ["-y", "@pipeworx/mcp-corporate-satire"]
    }
  }
}
```

Or run it directly to confirm it starts:

```bash
npx -y @pipeworx/mcp-corporate-satire
```

It speaks MCP over stdin/stdout and answers `initialize`/`tools/list`/`tools/call`
for **only** this pack's tools — none of the shared meta-tools the gateway
connection above adds. Same source, same tools, no ask_pipeworx routing.

## Using with ask_pipeworx

Instead of calling tools directly, you can ask questions in plain English —
this works on the pack endpoint above as well as on the full gateway:

```
ask_pipeworx({ question: "your question about Corporate Satire data" })
```

The gateway picks the right tool and fills the arguments automatically.

## More

- [Docs and guides](https://pipeworx.io/docs)
- [pipeworx.io](https://pipeworx.io)

## License

MIT
