---
name: code-biotherapeutics-site-lookup
description: >-
  Answer questions about Code Biotherapeutics — company details, the 3DNA non-viral delivery
  platform, pipeline programs, leadership, publications, partnerships and news — by calling
  the company's own Model Context Protocol server instead of scraping www.codebiotx.com.
api: Code Bio Site MCP Server
endpoint: https://www.codebiotx.com/_api/mcp
transport: streamable-http
protocol: MCP 2025-06-18
operations:
  - GetBusinessDetails
  - SearchInSite
  - GenerateVisitorToken
generated: '2026-08-09'
method: generated
source: mcp/code-biotherapeutics-mcp-tools-list.json
---

# Code Biotherapeutics site lookup

Code Biotherapeutics is a preclinical genetic medicine company. It publishes **no REST or
GraphQL API**. Its only machine-readable surface is the MCP server at
`https://www.codebiotx.com/_api/mcp`, advertised in
[its llms.txt](https://www.codebiotx.com/llms.txt). Use that instead of fetching HTML.

Every tool name and every parameter below was read from a live anonymous `tools/list` on
2026-08-09 and is recorded verbatim in `mcp/code-biotherapeutics-mcp-tools-list.json`.

## Connect

`POST` JSON-RPC 2.0 to the endpoint with
`Accept: application/json, text/event-stream`. No credential is required for `initialize`
or `tools/list`.

```json
{"jsonrpc":"2.0","id":1,"method":"initialize",
 "params":{"protocolVersion":"2025-06-18","capabilities":{},
           "clientInfo":{"name":"your-agent","version":"1.0"}}}
```

The server responds with `protocolVersion: 2025-06-18` and
`capabilities.tools.listChanged: true` — re-issue `tools/list` when a
`notifications/tools/list_changed` arrives.

## Step 1 — establish who you are talking to

Call **`GetBusinessDetails`** (no arguments). It returns the business name, description,
timezone, contact email and the physical address in Hatfield, Pennsylvania. Start here so
later answers are grounded in the site's own record rather than a search index.

## Step 2 — answer the actual question

Call **`SearchInSite`** with a single required string argument `searchTerm`.

```json
{"jsonrpc":"2.0","id":2,"method":"tools/call",
 "params":{"name":"SearchInSite","arguments":{"searchTerm":"3DNA delivery platform"}}}
```

Useful terms for this company: `3DNA`, `non-viral gene delivery`, `Duchenne Muscular
Dystrophy`, `Type 1 Diabetes`, `pipeline`, `leadership`, `board of directors`,
`publications`, `partnerships`, `open positions`.

`SearchInSite` covers the company's public pages only. It does not expose scientific,
preclinical or clinical datasets — none are published.

## Step 3 — only if you need to act, not read

`CallWixSiteAPI` and `ExecuteWixAPI` are **mutating** tools that call Wix business
solutions on a visitor's behalf. They require a `visitorToken` from
**`GenerateVisitorToken`** (no arguments, no credential). Do not invoke them to answer a
research question: for Code Biotherapeutics there is no storefront, booking or ordering
surface behind them, and `ExecuteWixAPI` requires you to declare `hasMutations` truthfully.

Prefer `SearchSiteApiDocs` first to learn what business solutions — if any — are actually
installed on this site before attempting any call.

## Boundaries

- Do not present this server as a Code Biotherapeutics product API. It is the Wix platform
  site MCP server, served from the company's host.
- No authentication is offered and none should be invented; there is no signup, no API key
  and no OAuth metadata (`/.well-known/oauth-authorization-server` → 400).
- Company contact for anything the site does not answer:
  <https://www.codebiotx.com/general-inquiries>.
