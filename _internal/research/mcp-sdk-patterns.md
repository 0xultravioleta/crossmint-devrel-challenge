# @modelcontextprotocol/sdk — Research R3

> Source: https://www.npmjs.com/package/@modelcontextprotocol/sdk (fetched via Playwright)
> Date: 2026-04-09 H+6
> Package version: **1.29.0** (published 10 days ago, 2026-03-30)
> License: MIT
> Repo: github.com/modelcontextprotocol/typescript-sdk
> Dependents: **40,815** (huge, well-established)

---

## R3 verdict

**Mature, stable SDK. Pin to `@modelcontextprotocol/sdk@^1.29.0` in package.json.**

---

## Core imports

```typescript
import { McpServer } from "@modelcontextprotocol/sdk";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio";  // stdio transport for local
```

## Required peer dependency

```bash
npm install @modelcontextprotocol/sdk zod
```

- Required peer: `zod` (both v3.25+ and v4 supported)
- Internal: `zod/v4`
- User code can import from `zod/v3` or `zod/v4`

## Transports

- **stdio** — for local, process-spawned integrations (Claude Desktop, Cursor, Claude Code, Continue.dev) **← WE USE THIS**
- **Streamable HTTP** — for remote servers (recommended for cloud/shared)
- **HTTP + SSE** — backwards compatibility only

## Primitives

- **Tools** — let LLMs call server functions (computation, side effects, network calls) **← WE USE THIS**
- **Resources** — expose read-only data
- **Prompts** — reusable templates

## Capabilities (advanced)

- **Sampling** — server asks client for LLM completions
- **Form elicitation** — structured input forms
- **URL elicitation** — OAuth / API key / payment flows via browser (could be interesting for Crossmint API key setup)
- **Tasks (experimental)** — long-running tool calls

## Example location

Runnable server examples at `src/examples/server/` in the typescript-sdk repo. Key files:
- `simpleStreamableHttp.ts` — HTTP transport example
- `toolWithSampleServer.ts` — tool + sampling capability
- `elicitationFormExample.ts`, `elicitationUrlExample.ts` — elicitation patterns

For stdio specifically, look for `src/examples/server/stdio*` or reference Saul's AutoJob MCP server which already uses stdio transport.

## Minimal stdio server skeleton (inferred from SDK surface)

```typescript
import { McpServer } from "@modelcontextprotocol/sdk";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio";
import { z } from "zod";

const server = new McpServer({
  name: "crossmint-wallets-mcp",
  version: "0.1.0",
});

// Register a tool
server.tool(
  "crossmint_create_wallet",
  "Create a Crossmint smart wallet for a given email and chain",
  {
    email: z.string().email(),
    chain: z.enum(["solana", "solana-devnet", "base", "base-sepolia"]),
  },
  async ({ email, chain }) => {
    // Implementation calls src/core/create-wallet.ts
    const wallet = await createWallet({ email, chain });
    return {
      content: [
        {
          type: "text",
          text: JSON.stringify(wallet, null, 2),
        },
      ],
    };
  }
);

// Start stdio transport
const transport = new StdioServerTransport();
await server.connect(transport);
```

**Note:** The exact API surface of `McpServer` constructor and `server.tool()` method needs to be verified by reading the SDK source or the docs/server.md file. The above is best-effort based on the SDK's conceptual overview. Phase 2B scaffold task includes a "verify McpServer API" subtask.

## MCP server best practices

1. **Log to stderr, not stdout.** The stdout stream is reserved for JSON-RPC transport. Any accidental stdout logging breaks the protocol.
2. **Return `content` array** in tool responses. Each item has `type` and `text` (or other content types).
3. **Use Zod schemas** for tool inputs. The SDK validates automatically.
4. **Errors as tool responses**, not exceptions. Return `{ content: [...], isError: true }` for graceful failures.
5. **Handle cancellation.** Long-running tools should check for abort signals.

## Claude Desktop config format

```json
{
  "mcpServers": {
    "crossmint-wallets": {
      "command": "npx",
      "args": ["-y", "crossmint-wallets-mcp"],
      "env": {
        "CROSSMINT_API_KEY": "sk_...",
        "CROSSMINT_RECOVERY_SECRET": "..."
      }
    }
  }
}
```

Config path on Windows: `%APPDATA%\Claude\claude_desktop_config.json`
Config path on macOS: `~/Library/Application Support/Claude/claude_desktop_config.json`

## Decisions driven by R3

1. **Pin `@modelcontextprotocol/sdk@^1.29.0`** in `package.json`
2. **Pin `zod@^3.25.0`** (compatible with both v3 and v4 API)
3. **Use stdio transport** (not Streamable HTTP) — matches Claude Desktop, Cursor, Claude Code
4. **Log to stderr only** inside the MCP server — verified non-negotiable
5. **Use `McpServer` class** with `server.tool(name, description, schema, handler)` pattern
6. **Phase 2B has a "verify McpServer API" task** — read the SDK source or docs/server.md to confirm the exact signatures before writing tools

## What I did not verify

- Exact signature of `McpServer` constructor options (capabilities, logging, etc.)
- Whether `server.tool()` accepts a 4-arg signature `(name, description, schema, handler)` or a different shape in v1.29.0
- Error handling patterns (should we throw or return `isError: true`?)
- Stdio transport buffering behavior on Windows vs macOS

These are OK to leave unverified because:
1. The AutoJob MCP server (Saul's existing production code) already uses `@modelcontextprotocol/sdk` with stdio transport and can be referenced directly during Phase 2B
2. The SDK is mature (40k+ dependents) and has stable patterns we can copy from open-source examples

## Research status

- [x] R1 — @crossmint/wallets-sdk API surface
- [x] R2 — @crossmint/lobster-cli library exports
- [x] R3 — @modelcontextprotocol/sdk minimal server patterns
- [ ] R4 — x402 payload format on Solana (deferred — low priority, SolanaWallet.sendTransaction handles the signing side; we only need to know what X-PAYMENT header content the endpoint expects)

R4 can be handled during Phase 2C build when we pick a target x402 endpoint. Not a blocker.
