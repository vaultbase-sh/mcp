<p align="center">
  <a href="https://vaultbase.dev">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://raw.githubusercontent.com/vaultbase-sh/vaultbase/main/assets/vaultbase-wordmark.svg">
      <img src="https://raw.githubusercontent.com/vaultbase-sh/vaultbase/main/assets/vaultbase-wordmark-light.svg" alt="Vaultbase" width="300">
    </picture>
  </a>
</p>

<h3 align="center"><code>@vaultbase/mcp</code></h3>

<p align="center">Connect any MCP client to a remote Vaultbase</p>

<p align="center">
  <a href="https://www.npmjs.com/package/@vaultbase/mcp"><img alt="npm version" src="https://img.shields.io/npm/v/%40vaultbase%2Fmcp?style=flat-square&color=e85a4f"></a>
  <a href="https://www.npmjs.com/package/@vaultbase/mcp"><img alt="npm downloads" src="https://img.shields.io/npm/dm/%40vaultbase%2Fmcp?style=flat-square&color=1b1a1a"></a>
  <img alt="Node 18+" src="https://img.shields.io/node/v/%40vaultbase%2Fmcp?style=flat-square&color=1b1a1a">
  <a href="https://github.com/vaultbase-sh/mcp/blob/main/LICENSE"><img alt="MIT license" src="https://img.shields.io/npm/l/%40vaultbase%2Fmcp?style=flat-square&color=1b1a1a"></a>
</p>

<p align="center">
  <a href="https://vaultbase.dev">Vaultbase</a> ·
  <a href="https://docs.vaultbase.dev">Docs</a> ·
  <a href="https://github.com/vaultbase-sh/vaultbase">Server</a> ·
  <a href="https://www.npmjs.com/package/@vaultbase/mcp">npm</a>
</p>

Stdio↔HTTP bridge for the [Vaultbase](https://vaultbase.dev) MCP server.

Vaultbase ships a first-party [Model Context Protocol](https://modelcontextprotocol.io)
server. When you run vaultbase locally you can wire it up over stdio with
`vaultbase mcp`. When the deployment lives on a remote host (or you don't
want to install the binary on every developer machine), this package
bridges the local MCP client (Claude Desktop, Cursor, Continue, Cline,
Zed, …) to Vaultbase's HTTP+SSE transport at `/api/v1/mcp/`.

```
client (stdio) ⇄ @vaultbase/mcp ⇄ vaultbase (HTTPS + SSE)
```

Pure TypeScript, ships as a single npm bin, requires Node ≥ 18. No Bun
runtime needed on the client.

## Install

```sh
npm install -g @vaultbase/mcp
# or use directly via npx
npx @vaultbase/mcp --url https://api.example.com --token vbat_…
```

## Claude Desktop config

`~/Library/Application Support/Claude/claude_desktop_config.json` (macOS)
or `%APPDATA%\Claude\claude_desktop_config.json` (Windows):

```json
{
  "mcpServers": {
    "vaultbase": {
      "command": "npx",
      "args": ["-y", "@vaultbase/mcp"],
      "env": {
        "VAULTBASE_URL": "https://api.example.com",
        "VAULTBASE_MCP_TOKEN": "vbat_eyJhbGciOiJIUzI1NiIs…"
      }
    }
  }
}
```

Mint a token via the admin UI (`/_/api-tokens`) or the CLI on the host:

```sh
vaultbase token mint --name "Claude Desktop" --scope mcp:read --ttl 1y
```

## Cursor / Continue / Cline / Zed

Same pattern — each editor has an MCP-server config block. Use
`command: "npx"`, `args: ["-y", "@vaultbase/mcp"]`, supply
`VAULTBASE_URL` + `VAULTBASE_MCP_TOKEN` env vars.

## Programmatic usage

```ts
import { runBridge } from "@vaultbase/mcp";

const handle = runBridge({
  url: "https://api.example.com",
  token: process.env.VAULTBASE_MCP_TOKEN!,
});
await handle.done;
```

The bridge does not parse or rewrite MCP messages — it only frames them
for whichever transport is on the other side. All MCP semantics
(tools, resources, prompts, scope enforcement, audit logging) stay in
the Vaultbase server.

## Compatibility

| `@vaultbase/mcp` | Vaultbase server |
|---|---|
| `0.1.x` | `>= 0.10.0` |

The Phase-3 HTTP+SSE transport ships in Vaultbase **0.10**. Earlier
servers only speak stdio — point Claude Desktop directly at the
`vaultbase` binary instead.

## License

MIT — © 2026 Vaultbase contributors
