# 03 — MCP Build & Skill Plan

> **Role:** MCP Build & Skill Planner
> **Owner:** Claude (direct execution)
> **Mandate:** Plan the full MCP server build (scaffold + 3 remaining tools + wiring + Claude Desktop smoke test) + the lobster.cash skill (Artifact 2)
> **Scope:** Phases 2B, 2D, 2E, 2F, 2G (H+7 → H+22, ~14 hours of execution)
> **Depends on:** `01-research-plan.md`, `02-critical-path-plan.md`
> **Line target:** 900-1200 (biggest plan, hard cap 1200 per PM)

---

## Framing discipline

- MCP server is positioned as the MCP-native COMPANION to lobster.cash — not a replacement, not a competitor
- Faremeter is the preferred facilitator in docs; Ultravioleta facilitator is NOT named
- No hardcoded API keys, recovery secrets, or wallet addresses in committed files
- Repo name: `crossmint-wallets-mcp` (Saul's personal GitHub: `0xultravioleta/crossmint-wallets-mcp`)
- Skill repo name: `crossmint-cpi-skill` (Saul's personal GitHub: `0xultravioleta/crossmint-cpi-skill`)
- License: MIT for both repos (so Crossmint can fork easily)

---

## What this plan is NOT

- NOT the plan for Tool 4 `pay_x402_endpoint` — that's `02-critical-path-plan.md`
- NOT the plan for the blog post, Twitter thread, or campaign proposal — that's `04-content-and-campaign-plan.md`
- NOT the plan for submission polish, smoke testing from clean state, or the email to Adolfo — that's `05-integration-qa-and-risk-plan.md`
- NOT a plan to modify Crossmint SDK or fork their repos
- NOT a plan to implement a new lobster.cash SDK or skill format — just use the existing `@crossmint/lobster-cli@3.0.8` as-is
- NOT a plan to publish to npm as `@crossmint/...` scope (we don't have permission) — ship under `0xultravioleta` scope or unscoped

---

## Dependencies on other plans

- **Depends on:** `01-research-plan.md` (R1 SDK surface, R3 MCP SDK patterns, R2 lobster-cli context), `02-critical-path-plan.md` (Tool 4 is wired in during Phase 2E)
- **Provides to:** `04-content-and-campaign-plan.md` (the blog post needs the demo to exist), `05-integration-qa-and-risk-plan.md` (integration needs both artifacts complete)

---

## Reference — Saul's existing assets

- **AutoJob MCP server:** `z:\ultravioleta\dao\autojob\` — known-good stdio transport + `McpServer` pattern. READ THIS during Phase 2B scaffold task before writing `src/mcp/server.ts`.
- **Execution Market MCP server:** `z:\ultravioleta\dao\execution-market\mcp_server\` — Python reference (FastMCP). Ignore for TypeScript path but useful as mental model.
- **Karmakadabra OpenClaw skills:** `z:\ultravioleta\dao\karmakadabra\openclaw\skills\` — reference for skill format structure, particularly `kk-x402` and `kk-wallet` as the closest analogs to `crossmint-cpi-skill`.

---

## Phase 1 — Scaffold (Phase 2B in BUILD-PLAN) — H+7 → H+8 (60 min)

### Task 1.1 — Verify the existing partial scaffold

- **Action:** Check the existing files at `z:\crossmint-wallets-mcp\` — these were created before the concept pivot:
  - `package.json` (created)
  - `tsconfig.json` (created)
  - `.gitignore` (created)
  - `LICENSE` (MIT, created)
  - `.git/` (initialized)
- **Command:** `ls -la z:/crossmint-wallets-mcp/ && cat z:/crossmint-wallets-mcp/package.json`
- **Time budget:** 5 minutes
- **Decision:** If files match the concept v2 structure, keep them. If not (e.g., package name is wrong or SDK version is outdated), update.
- **Fallback:** Delete and re-scaffold if anything is structurally broken

### Task 1.2 — Update `package.json` to match the locked concept

- **Action:** Ensure `package.json` has:
  - `"name": "crossmint-wallets-mcp"` (unscoped) OR `"@0xultravioleta/crossmint-wallets-mcp"` (scoped under Saul's npm account)
  - `"version": "0.1.0"`
  - `"type": "module"` (ESM)
  - `"bin": { "crossmint-wallets-mcp": "dist/mcp/server.js" }`
  - `"dependencies"`:
    - `@modelcontextprotocol/sdk@^1.29.0`
    - `@crossmint/wallets-sdk@^1.0.7`
    - `@solana/web3.js@^1.95.0`
    - `@solana/spl-token@^0.4.9`
    - `zod@^3.25.0`
  - `"devDependencies"`:
    - `@types/node@^20.10.0`
    - `tsx@^4.7.0`
    - `typescript@^5.4.0`
  - `"scripts"`:
    - `"build": "tsc"`
    - `"start": "node dist/mcp/server.js"`
    - `"dev": "tsc --watch"`
    - `"demo": "tsx demo/smoke-test.ts"`
    - `"clean": "rimraf dist"`
- **File:** `z:\crossmint-wallets-mcp\package.json`
- **Time budget:** 10 minutes
- **Fallback:** If `@0xultravioleta` scope publish fails later, rename to unscoped `crossmint-wallets-mcp` — npm should allow unscoped if name is available

### Task 1.3 — Install dependencies

- **Action:** `cd z:/crossmint-wallets-mcp && pnpm install`
- **Time budget:** 5 minutes
- **Fallback:** If pnpm fails, use `npm install`. If that fails, pin exact versions in package.json.

### Task 1.4 — Verify TypeScript compilation works on an empty project

- **Action:** Create a placeholder `src/index.ts` with `export {};`, run `pnpm build`, verify `dist/index.js` exists
- **Time budget:** 5 minutes
- **Fallback:** Adjust `tsconfig.json` if there are module resolution errors

### Task 1.5 — Create `src/core/` and `src/mcp/` skeleton files

- **Files to create:**
  - `src/core/client.ts` — initialize Crossmint client (stub)
  - `src/core/types.ts` — shared TypeScript types
  - `src/core/create-wallet.ts` — stub function
  - `src/core/get-balance.ts` — stub function
  - `src/core/transfer-token.ts` — stub function
  - `src/core/pay-x402-endpoint.ts` — stub function (populated in Phase 2C)
  - `src/mcp/server.ts` — minimal `McpServer` with stdio transport (stub)
  - `src/mcp/tools.ts` — tool registration (stub)
  - `src/mcp/errors.ts` — standardized error responses (stub)
  - `src/index.ts` — library exports
- **Time budget:** 15 minutes
- **Fallback:** N/A — this is skeleton writing

### Task 1.6 — Write `src/core/client.ts` with Crossmint client initialization

- **Code:**
  ```typescript
  import { createCrossmint, CrossmintWallets } from "@crossmint/wallets-sdk";

  export interface CrossmintConfig {
    apiKey: string;
    recoverySecret: string;
  }

  export function getConfig(): CrossmintConfig {
    const apiKey = process.env.CROSSMINT_API_KEY;
    const recoverySecret = process.env.CROSSMINT_RECOVERY_SECRET;
    if (!apiKey) throw new Error("CROSSMINT_API_KEY env var is required");
    if (!recoverySecret) throw new Error("CROSSMINT_RECOVERY_SECRET env var is required");
    return { apiKey, recoverySecret };
  }

  export function getWalletsClient() {
    const { apiKey } = getConfig();
    const crossmint = createCrossmint({ apiKey });
    return CrossmintWallets.from(crossmint);
  }
  ```
- **Time budget:** 10 minutes
- **Fallback:** If the SDK's import paths have changed in v1.0.7, consult the README from R1 research

### Task 1.7 — Write `.env.example`

- **Content:**
  ```
  # Create a server API key at https://www.crossmint.com/console
  # Required scopes: wallets.create, wallets.read, wallets:transactions.create, wallets:transactions.sign, wallets:balance.read
  CROSSMINT_API_KEY=

  # Server recovery signer secret (any random 32+ character string — keep this private)
  CROSSMINT_RECOVERY_SECRET=

  # Default chain for new wallets (solana | solana-devnet | base | base-sepolia)
  DEFAULT_CHAIN=solana-devnet

  # RPC endpoint for Solana (override if you want to use a private RPC)
  SOLANA_RPC_URL=https://api.devnet.solana.com
  ```
- **File:** `z:\crossmint-wallets-mcp\.env.example`
- **Time budget:** 5 minutes
- **Fallback:** N/A

### Task 1.8 — Create `demo/smoke-test.ts` skeleton

- **Content:**
  ```typescript
  import { createWallet } from "../src/core/create-wallet.js";
  import { getBalance } from "../src/core/get-balance.js";
  import { transferToken } from "../src/core/transfer-token.js";
  import { payX402Endpoint } from "../src/core/pay-x402-endpoint.js";

  async function main() {
    console.error("=== Crossmint Wallets MCP smoke test ===");
    // Steps populated during later phases
  }

  main().catch((err) => {
    console.error("Smoke test failed:", err);
    process.exit(1);
  });
  ```
- **File:** `z:\crossmint-wallets-mcp\demo\smoke-test.ts`
- **Time budget:** 5 minutes

### Task 1.9 — Verify `tsc --noEmit` passes on all stubs

- **Command:** `cd z:/crossmint-wallets-mcp && pnpm tsc --noEmit`
- **Expected:** zero errors
- **Time budget:** 5 minutes
- **Fallback:** Fix any type errors; likely cause is missing exports in stub files

### Task 1.10 — Create the companion skill repo skeleton

- **Files:**
  - `z:\crossmint-cpi-skill\README.md` (skeleton)
  - `z:\crossmint-cpi-skill\SKILL.md` (skeleton)
  - `z:\crossmint-cpi-skill\LICENSE` (MIT)
  - `z:\crossmint-cpi-skill\package.json` (minimal, for discoverability)
  - `z:\crossmint-cpi-skill\.gitignore`
- **Command:** `mkdir -p z:/crossmint-cpi-skill && cd z:/crossmint-cpi-skill && git init -b main`
- **Time budget:** 10 minutes

### Phase 1 gate (H+8)

- **Criteria:**
  - [ ] `pnpm tsc --noEmit` passes on the MCP server repo
  - [ ] `pnpm demo` runs without crashing (even though all functions are stubs)
  - [ ] `crossmint-cpi-skill` repo initialized with skeleton files
- **Pass action:** Proceed to Phase 2 (critical path — Tool 4)
- **Fail action:** Debug scaffold issues; max 30 minutes extra allowed

---

## Phase 2 — Tool 4 `pay_x402_endpoint` (H+8 → H+12)

**Delegated to `02-critical-path-plan.md`.** This phase's ownership belongs to the Critical Path Planner. The MCP Build Planner's role here is only to ensure the output of Tool 4 integrates cleanly into the MCP server in Phase 5 (wiring).

### Integration contract

- **Input signature:** `{ email: string, url: string, maxUsdc?: number, headers?: Record<string, string>, jsonBody?: unknown }`
- **Output signature:** `{ transactionSignature: string, responseStatus: number, responseBody: unknown, explorerLink: string }`
- **Export:** `export async function payX402Endpoint(opts): Promise<...>` from `src/core/pay-x402-endpoint.ts`

---

## Phase 3 — Tool 1 `create_wallet` (Phase 2D Tool 1 in BUILD-PLAN) — H+12 → H+13 (60 min)

### Task 3.1 — Implement `src/core/create-wallet.ts`

- **Code:**
  ```typescript
  import { getWalletsClient, getConfig } from "./client.js";
  import type { Chain, CreateWalletResult } from "./types.js";

  export async function createWallet(opts: {
    email: string;
    chain: Chain;
  }): Promise<CreateWalletResult> {
    const { email, chain } = opts;
    const { recoverySecret } = getConfig();
    const wallets = getWalletsClient();

    const wallet = await wallets.createWallet({
      chain,
      recovery: { type: "server", secret: recoverySecret },
    });

    return {
      email,
      chain,
      address: wallet.address,
      explorerLink: getExplorerLink(wallet.address, chain),
    };
  }

  function getExplorerLink(address: string, chain: Chain): string {
    switch (chain) {
      case "solana": return `https://explorer.solana.com/address/${address}`;
      case "solana-devnet": return `https://explorer.solana.com/address/${address}?cluster=devnet`;
      case "base": return `https://basescan.org/address/${address}`;
      case "base-sepolia": return `https://sepolia.basescan.org/address/${address}`;
      default: return "";
    }
  }
  ```
- **File:** `z:\crossmint-wallets-mcp\src\core\create-wallet.ts`
- **Time budget:** 25 minutes
- **Fallback:** If `wallets.createWallet` signature differs from R1 findings, check the live SDK source at `node_modules/@crossmint/wallets-sdk/dist/` for the exact types

### Task 3.2 — Write types in `src/core/types.ts`

- **Content:**
  ```typescript
  export type Chain =
    | "solana"
    | "solana-devnet"
    | "base"
    | "base-sepolia";

  export interface CreateWalletResult {
    email: string;
    chain: Chain;
    address: string;
    explorerLink: string;
  }

  export interface BalanceResult {
    email: string;
    chain: Chain;
    address: string;
    balances: Array<{
      symbol: string;
      amount: string;
      decimals: number;
    }>;
  }

  export interface TransferResult {
    email: string;
    chain: Chain;
    from: string;
    to: string;
    token: string;
    amount: string;
    transactionSignature: string;
    explorerLink: string;
  }

  export interface PayX402Result {
    email: string;
    url: string;
    transactionSignature: string;
    responseStatus: number;
    responseBody: unknown;
    explorerLink: string;
  }
  ```
- **Time budget:** 15 minutes

### Task 3.3 — Test `create_wallet` in isolation

- **Action:** Update `demo/smoke-test.ts` to call `createWallet` with a test email on `solana-devnet`
- **Expected output:** Wallet address logged
- **Time budget:** 10 minutes
- **Fallback:** If the wallet already exists for that email, that's OK — it should be idempotent

### Task 3.4 — Handle "wallet already exists" case

- **Action:** If `createWallet` throws a "wallet exists" error, catch it and call `wallets.getWallet(address, { chain })` instead. Return the same shape either way.
- **Time budget:** 10 minutes
- **Fallback:** If no "getWallet by email" exists, cache created wallets locally in `demo/test-wallets.json` (gitignored)

### Phase 3 gate (H+13)

- **Criteria:** `demo/smoke-test.ts` calls `createWallet({ email: "test@example.com", chain: "solana-devnet" })` and prints a Solana address
- **Pass:** Proceed to Phase 4

---

## Phase 4 — Tool 2 `get_balance` (H+13 → H+14, 60 min)

### Task 4.1 — Implement `src/core/get-balance.ts`

- **Code:**
  ```typescript
  import { getWalletsClient } from "./client.js";
  import type { Chain, BalanceResult } from "./types.js";

  export async function getBalance(opts: {
    email: string;
    chain: Chain;
    tokens?: string[];
  }): Promise<BalanceResult> {
    const { email, chain } = opts;
    const wallets = getWalletsClient();

    // Resolve wallet by email (via locally cached address or Crossmint API)
    const address = await resolveAddressByEmail(email, chain);
    const wallet = await wallets.getWallet(address, { chain });

    const balances = await wallet.balances();

    return {
      email,
      chain,
      address,
      balances: formatBalances(balances),
    };
  }
  ```
- **Time budget:** 30 minutes
- **Fallback:** If `wallet.balances()` returns a different shape than R1 findings, inspect at runtime and adapt

### Task 4.2 — Helper: `resolveAddressByEmail`

- **Implementation:** Cache wallets in `demo/test-wallets.json` (gitignored). In production MCP server usage, the address should be derivable from the email via a Crossmint API call, but we cache locally for the smoke test.
- **Time budget:** 15 minutes

### Task 4.3 — Update smoke test

- **Action:** Add a call to `getBalance` after `createWallet`. Expected: a wallet with 0 balance initially, or whatever was funded via faucet.
- **Time budget:** 10 minutes

### Task 4.4 — Handle empty balance case

- **Action:** Return an empty `balances[]` array if no tokens are held. Do not throw.
- **Time budget:** 5 minutes

### Phase 4 gate (H+14)

- **Criteria:** Smoke test prints a balance for the test wallet (even if 0)
- **Pass:** Proceed to Phase 5

---

## Phase 5 — Tool 3 `transfer_token` (H+14 → H+16, 120 min)

### Task 5.1 — Implement `src/core/transfer-token.ts`

- **Code:**
  ```typescript
  import { getWalletsClient } from "./client.js";
  import type { Chain, TransferResult } from "./types.js";

  export async function transferToken(opts: {
    email: string;
    chain: Chain;
    to: string;
    token: string;
    amount: string;
  }): Promise<TransferResult> {
    const { email, chain, to, token, amount } = opts;
    const wallets = getWalletsClient();
    const address = await resolveAddressByEmail(email, chain);
    const wallet = await wallets.getWallet(address, { chain });

    const tx = await wallet.send(to, token, amount);

    return {
      email,
      chain,
      from: address,
      to,
      token,
      amount,
      transactionSignature: tx.hash,
      explorerLink: tx.explorerLink,
    };
  }
  ```
- **Time budget:** 30 minutes

### Task 5.2 — Handle insufficient balance

- **Action:** Catch `wallet.send` errors that indicate insufficient balance, return a clear error message
- **Time budget:** 15 minutes

### Task 5.3 — Handle invalid address

- **Action:** Validate address format before calling `wallet.send`. Use chain-specific validators (PublicKey for Solana, isAddress for EVM)
- **Time budget:** 15 minutes

### Task 5.4 — Test transfer on Devnet

- **Action:** Transfer 0.01 USDC from test wallet to another test wallet (or burn address). Confirm the explorer link shows the transaction.
- **Time budget:** 20 minutes
- **Fallback:** If Devnet USDC faucet didn't fund the wallet, skip this smoke test and document in README as "requires Devnet funding"

### Task 5.5 — Update smoke test

- **Action:** Add a transfer step to `demo/smoke-test.ts`
- **Time budget:** 10 minutes

### Task 5.6 — Parallel: start wiring the MCP server (Phase 6)

- **Action:** While waiting for transfer smoke test to confirm, begin writing `src/mcp/server.ts` skeleton
- **Time budget:** 30 minutes (overlapping with 5.4)

### Phase 5 gate (H+16)

- **Criteria:** Smoke test shows 3 tools working: create → balance → transfer
- **Pass:** Proceed to Phase 6 (MCP wiring)

---

## Phase 6 — MCP server wiring (Phase 2E in BUILD-PLAN) — H+16 → H+18 (120 min)

### Task 6.1 — Read Saul's AutoJob MCP server for reference

- **Action:** Navigate to `z:\ultravioleta\dao\autojob\` and read the MCP server entry point
- **Command:** `find z:/ultravioleta/dao/autojob -name "*.ts" -path "*mcp*" | head -5`
- **Extract:** the exact `McpServer` constructor usage, `server.tool()` signature, and stdio transport init
- **Time budget:** 15 minutes
- **Fallback:** If AutoJob uses Python or an older MCP SDK version, fall back to the TypeScript SDK examples at `github.com/modelcontextprotocol/typescript-sdk/tree/main/src/examples/server/`

### Task 6.2 — Implement `src/mcp/server.ts`

- **Code:**
  ```typescript
  #!/usr/bin/env node
  import { McpServer } from "@modelcontextprotocol/sdk";
  import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio";
  import { registerTools } from "./tools.js";

  async function main() {
    const server = new McpServer({
      name: "crossmint-wallets-mcp",
      version: "0.1.0",
    });

    registerTools(server);

    const transport = new StdioServerTransport();
    await server.connect(transport);

    // Log to stderr only (stdout is reserved for JSON-RPC)
    console.error("[crossmint-wallets-mcp] connected via stdio");
  }

  main().catch((err) => {
    console.error("[crossmint-wallets-mcp] fatal:", err);
    process.exit(1);
  });
  ```
- **Time budget:** 20 minutes
- **Fallback:** If `McpServer` signature differs in v1.29.0, adjust per the actual SDK API

### Task 6.3 — Implement `src/mcp/tools.ts`

- **Code (pattern for one tool, repeat for all 4):**
  ```typescript
  import type { McpServer } from "@modelcontextprotocol/sdk";
  import { z } from "zod";
  import { createWallet } from "../core/create-wallet.js";
  import { getBalance } from "../core/get-balance.js";
  import { transferToken } from "../core/transfer-token.js";
  import { payX402Endpoint } from "../core/pay-x402-endpoint.js";
  import { formatError } from "./errors.js";

  const chainEnum = z.enum(["solana", "solana-devnet", "base", "base-sepolia"]);

  export function registerTools(server: McpServer) {
    server.tool(
      "crossmint_create_wallet",
      "Create a Crossmint smart wallet for a given email and chain.",
      {
        email: z.string().email(),
        chain: chainEnum,
      },
      async ({ email, chain }) => {
        try {
          const result = await createWallet({ email, chain });
          return {
            content: [{ type: "text", text: JSON.stringify(result, null, 2) }],
          };
        } catch (err) {
          return formatError(err);
        }
      }
    );

    server.tool(
      "crossmint_get_balance",
      "Get token balances for a Crossmint wallet.",
      {
        email: z.string().email(),
        chain: chainEnum,
        tokens: z.array(z.string()).optional(),
      },
      async ({ email, chain, tokens }) => {
        try {
          const result = await getBalance({ email, chain, tokens });
          return { content: [{ type: "text", text: JSON.stringify(result, null, 2) }] };
        } catch (err) {
          return formatError(err);
        }
      }
    );

    server.tool(
      "crossmint_transfer_token",
      "Transfer tokens from a Crossmint wallet to another address.",
      {
        email: z.string().email(),
        chain: chainEnum,
        to: z.string(),
        token: z.string(),
        amount: z.string(),
      },
      async ({ email, chain, to, token, amount }) => {
        try {
          const result = await transferToken({ email, chain, to, token, amount });
          return { content: [{ type: "text", text: JSON.stringify(result, null, 2) }] };
        } catch (err) {
          return formatError(err);
        }
      }
    );

    server.tool(
      "crossmint_pay_x402_endpoint",
      "Pay an x402-protected HTTP endpoint from a Crossmint wallet and return the response.",
      {
        email: z.string().email(),
        url: z.string().url(),
        maxUsdc: z.number().optional(),
        headers: z.record(z.string()).optional(),
        jsonBody: z.any().optional(),
      },
      async ({ email, url, maxUsdc, headers, jsonBody }) => {
        try {
          const result = await payX402Endpoint({ email, url, maxUsdc, headers, jsonBody });
          return { content: [{ type: "text", text: JSON.stringify(result, null, 2) }] };
        } catch (err) {
          return formatError(err);
        }
      }
    );
  }
  ```
- **Time budget:** 40 minutes
- **Fallback:** If any Zod schema fails validation, adjust the schema to match the actual input shape

### Task 6.4 — Implement `src/mcp/errors.ts`

- **Code:**
  ```typescript
  export function formatError(err: unknown) {
    const message = err instanceof Error ? err.message : String(err);
    return {
      content: [{ type: "text", text: `Error: ${message}` }],
      isError: true,
    };
  }
  ```
- **Time budget:** 5 minutes

### Task 6.5 — Build and verify the MCP server binary

- **Command:**
  ```bash
  cd z:/crossmint-wallets-mcp
  pnpm build
  ls dist/mcp/server.js  # should exist
  chmod +x dist/mcp/server.js  # ensure executable (Linux/macOS)
  ```
- **Time budget:** 10 minutes
- **Fallback:** If `dist/mcp/server.js` is missing, fix `tsconfig.json` paths

### Task 6.6 — Test the MCP server locally via a JSON-RPC handshake

- **Action:** Pipe a minimal MCP handshake into the server stdio and verify responses
- **Command:**
  ```bash
  echo '{"jsonrpc":"2.0","id":1,"method":"initialize","params":{"protocolVersion":"2024-11-05","capabilities":{},"clientInfo":{"name":"test","version":"0.0.1"}}}' | node dist/mcp/server.js
  ```
- **Expected:** A JSON-RPC response with server info
- **Time budget:** 15 minutes
- **Fallback:** If the handshake fails, add stderr logging inside the tool handlers to diagnose

### Task 6.7 — Verify `tools/list` returns all 4 tools

- **Command:**
  ```bash
  echo '{"jsonrpc":"2.0","id":2,"method":"tools/list"}' | node dist/mcp/server.js
  ```
- **Expected:** JSON array with 4 tool definitions
- **Time budget:** 10 minutes

### Phase 6 gate (H+18)

- **Criteria:**
  - [ ] `dist/mcp/server.js` exists and is executable
  - [ ] Responds to `initialize` MCP method
  - [ ] `tools/list` returns all 4 tools
  - [ ] At least one `tools/call` invocation returns a valid response (mock or real)
- **Pass:** Proceed to Phase 7 (Claude Desktop smoke test)

---

## Phase 7 — Claude Desktop smoke test (Phase 2F in BUILD-PLAN) — H+18 → H+20 (120 min)

### Task 7.1 — Publish to npm (first publish)

- **Action:** Publish `crossmint-wallets-mcp@0.1.0` so `npx crossmint-wallets-mcp` works
- **Command:**
  ```bash
  cd z:/crossmint-wallets-mcp
  pnpm login  # if not already logged in
  pnpm publish --access public
  ```
- **Time budget:** 15 minutes
- **Fallback:** If the package name is taken, rename to `@0xultravioleta/crossmint-wallets-mcp` and republish. If publish fails, fall back to running via local path in the Claude Desktop config.

### Task 7.2 — Verify `npx crossmint-wallets-mcp` works from a fresh shell

- **Command:** `npx crossmint-wallets-mcp --help` (or similar) — just confirm the binary resolves
- **Time budget:** 5 minutes
- **Fallback:** If `--help` isn't implemented, just pipe the `initialize` JSON-RPC and verify response

### Task 7.3 — Add the MCP server to Claude Desktop config

- **Action:** Edit `%APPDATA%\Claude\claude_desktop_config.json` on Windows
- **Config block:**
  ```json
  {
    "mcpServers": {
      "crossmint-wallets": {
        "command": "npx",
        "args": ["-y", "crossmint-wallets-mcp"],
        "env": {
          "CROSSMINT_API_KEY": "sk_staging_...",
          "CROSSMINT_RECOVERY_SECRET": "test-recovery-secret-do-not-use-in-prod"
        }
      }
    }
  }
  ```
- **Time budget:** 10 minutes
- **Security note:** Use a staging API key, not production. Use a non-sensitive recovery secret for the demo.

### Task 7.4 — Restart Claude Desktop and verify the MCP server shows up

- **Action:** Close and reopen Claude Desktop. Check the MCP server indicator (usually in the bottom of the chat input).
- **Time budget:** 5 minutes
- **Fallback:** If the server doesn't show up, check Claude Desktop's log files for errors

### Task 7.5 — Test each tool via Claude Desktop chat

- **Prompts to test:**
  1. "Create a Crossmint smart wallet on Solana Devnet for test@example.com"
  2. "What's the USDC balance of that wallet?"
  3. "Send 0.001 USDC from that wallet to <some-devnet-address>"
  4. "Pay the x402 endpoint at <test-endpoint-url> from that wallet with a max of 0.01 USDC"
- **Expected:** Each call returns a valid response via the MCP server
- **Time budget:** 40 minutes (including debugging)
- **Fallback:** If tool 4 fails in Claude Desktop but works in the local smoke test, it's probably an env var issue in the Claude Desktop config

### Task 7.6 — Capture screenshots for the blog post

- **Action:** Take screenshots at each step for use in `content/blog-post.md`
- **Files to save:** `content/screenshots/01-wallet-created.png`, `02-balance-checked.png`, `03-transfer-sent.png`, `04-x402-paid.png`
- **Time budget:** 15 minutes

### Task 7.7 — Record the demo video (optional if time)

- **Action:** Screen capture a 60-90 second walkthrough of Claude Desktop using the MCP server
- **Tool:** Any screen recorder (OBS, QuickTime, macOS screen record, Windows Game Bar)
- **File:** `content/video-demo.mp4` (will be linked from README, not committed to git)
- **Time budget:** 30 minutes
- **Fallback:** Skip the video if time pressured; screenshots are enough

### Phase 7 gate (H+20)

- **Criteria:**
  - [ ] `npx crossmint-wallets-mcp` runs from a fresh shell
  - [ ] Claude Desktop successfully calls at least 3 of 4 tools
  - [ ] Screenshots captured for the blog post
- **Pass:** Proceed to Phase 8 (lobster.cash skill)
- **Fail:** If Claude Desktop can't talk to the server, debug config paths and env var injection

---

## Phase 8 — Lobster.cash skill (Phase 2G in BUILD-PLAN, Artifact 2) — H+20 → H+22 (120 min)

### Task 8.1 — Read the lobster.cash Skill Compatibility Guide one more time

- **File:** already fetched during research, stored in `_internal/brainstorm/lobster-cash-deep-dive.md`
- **Key requirements per compatibility guide:**
  1. Wallet precheck — Does a wallet exist before payment flows?
  2. Payment action description — Define the transaction intent (merchant, amount, reason)
  3. Payment status and error handling — Handle: wallet not configured, insufficient balance, payment failure, pending confirmation
  4. Do NOT generate wallets or request private keys
  5. Do NOT hardcode internal lobster.cash action names
  6. Delegate payment execution to lobster.cash (via `lobstercash crypto x402 fetch`)
- **Time budget:** 10 minutes

### Task 8.2 — Write `SKILL.md` with the decision tree

- **Content outline:**
  ```markdown
  ---
  name: crossmint-cpi-solana
  description: Teaches AI agents the Solana CPI inner instruction nuance when paying x402 endpoints from Crossmint smart wallets via lobster.cash
  version: 0.1.0
  ---

  # Crossmint CPI Skill for Solana x402 Payments

  ## When to use this skill

  Use this skill when ALL of the following are true:
  1. The agent needs to pay an x402-paywalled HTTP endpoint
  2. The payment is on Solana (mainnet or devnet)
  3. The agent has `@crossmint/lobster-cli` installed

  ## Step 1: Wallet precheck

  Before any payment, check if the agent has a configured Crossmint wallet:

  ```bash
  lobstercash status
  ```

  If no wallet exists, run `lobstercash setup` first.

  ## Step 2: Payment action description

  Describe the payment intent to the user (or log it):
  - Target URL: <url>
  - Max amount: <amount> USDC
  - Reason: <why-we-need-to-pay>

  ## Step 3: Execute the payment

  Delegate to lobster.cash's built-in x402 flow:

  ```bash
  lobstercash crypto x402 fetch <url>
  ```

  ## Step 4: Solana smart wallet CPI note

  Crossmint smart wallets on Solana execute token transfers via Cross-Program Invocation (CPI). This means the USDC transfer is NOT a top-level transaction instruction — it's nested inside the smart wallet program's execution.

  Most x402 facilitators scan only top-level instructions and will reject Crossmint smart wallet payments with "no transfer found". lobster.cash (via its Faremeter integration) handles this correctly by scanning CPI inner instructions.

  **This is the unique technical insight this skill teaches:** other skills may work generically, but this one ensures agents understand WHY lobster.cash works for Crossmint smart wallets when naive x402 clients don't.

  ## Step 5: Handle errors

  - **Wallet not configured:** run `lobstercash setup` and retry
  - **Insufficient balance:** notify user, suggest funding via Circle faucet (devnet) or `lobstercash crypto deposit` (mainnet)
  - **Payment failure:** check `lobstercash crypto tx status --id <txId>`
  - **Pending confirmation:** wait up to 60 seconds and re-check status

  ## What this skill does NOT do

  - Does NOT implement payment logic (delegated to lobster.cash)
  - Does NOT access private keys (that's lobster.cash's job)
  - Does NOT support Ethereum/Base EVM (Solana only; use a different skill for EVM)
  - Does NOT replace lobster.cash — this is a thin educational adapter on top of it
  ```
- **File:** `z:\crossmint-cpi-skill\SKILL.md`
- **Time budget:** 45 minutes

### Task 8.3 — Write `README.md` for the skill repo

- **Content:**
  - Title + 1-sentence description
  - Install path: `npx skills add https://github.com/0xultravioleta/crossmint-cpi-skill --global --yes`
  - Prerequisites: `@crossmint/lobster-cli` installed, Crossmint wallet configured
  - What the skill teaches (CPI inner instruction nuance)
  - How it differs from other lobster.cash skills
  - Certification status (pending or certified)
  - Link to the Crossmint Wallets MCP Server (the sibling artifact)
- **File:** `z:\crossmint-cpi-skill\README.md`
- **Time budget:** 25 minutes

### Task 8.4 — Write minimal `package.json` for the skill repo

- **Content:**
  ```json
  {
    "name": "crossmint-cpi-skill",
    "version": "0.1.0",
    "description": "A lobster.cash-compatible skill that teaches AI agents the Solana CPI inner instruction nuance when paying x402 endpoints from Crossmint smart wallets.",
    "keywords": ["crossmint", "lobster-cash", "skill", "x402", "solana", "cpi"],
    "license": "MIT",
    "repository": {
      "type": "git",
      "url": "https://github.com/0xultravioleta/crossmint-cpi-skill.git"
    }
  }
  ```
- **Time budget:** 5 minutes

### Task 8.5 — Test the skill install path

- **Action:** Try `npx skills add https://github.com/0xultravioleta/crossmint-cpi-skill --global --yes` from a fresh shell
- **Note:** This may not work until the repo is public on GitHub (Phase 6 in integration). Skip if not ready.
- **Time budget:** 10 minutes
- **Fallback:** Skip if GitHub push not done yet; verify during integration phase

### Task 8.6 — Draft the Fede certification request message

- **Content (Spanish, for Telegram):**
  ```
  hey fede, termine el skill que comentamos. es compatible con lobster.cash — delega pagos al cli via lobstercash crypto x402 fetch.

  lo que hace: enseña a agentes el matiz del CPI inner instruction en solana smart wallets cuando pagan x402. ninguno de los 13 skills del directorio cubre eso y es el gap mas dificil de explicar en el ecosistema.

  repo: https://github.com/0xultravioleta/crossmint-cpi-skill

  crees que lo pueden certificar para el directorio de lobster.cash/skills? si quieres te mando el doc completo con el razonamiento.
  ```
- **File:** `z:\crossmint-cpi-skill\_submission\fede-message.md` (NOT committed to git — kept as a draft reference)
- **Time budget:** 15 minutes
- **Fallback:** N/A

### Task 8.7 — Commit the skill repo

- **Commit message:**
  ```
  feat: initial skill implementation

  - SKILL.md with decision tree for Solana x402 payments via CPI
  - README.md with install path and certification status
  - package.json for discoverability
  - MIT licensed

  Compatible with lobster.cash's Skill Compatibility Guide:
  - Wallet precheck documented
  - Payment action description required
  - Error handling for 4 failure modes
  - Delegates execution to @crossmint/lobster-cli
  - Does not touch private keys
  ```
- **Time budget:** 10 minutes

### Phase 8 gate (H+22)

- **Criteria:**
  - [ ] `z:\crossmint-cpi-skill\SKILL.md` exists and covers all 3 required behaviors
  - [ ] README.md and package.json are ready for public push
  - [ ] Fede certification message drafted
- **Pass:** Both artifacts are functionally complete. Hand off to Content Planner for blog post.
- **Fail:** If the skill format is ambiguous, contact Fede on Telegram for clarification (he's explicitly the cert contact)

---

## Handoff to Content Planner

At H+22, both artifacts exist in functional form:

- **Artifact 1:** `z:\crossmint-wallets-mcp\` with 4 working MCP tools, Claude Desktop smoke tested, published to npm as `crossmint-wallets-mcp@0.1.0`
- **Artifact 2:** `z:\crossmint-cpi-skill\` with SKILL.md, README.md, package.json, ready to push to GitHub

The Content Planner (`04-content-and-campaign-plan.md`) can now write the blog post using real screenshots, real transaction signatures, and real tool outputs.

---

## Parallel execution opportunities

- **Phase 2 (Tool 4) and Phase 6 (MCP wiring) CANNOT run in parallel** — wiring needs all 4 tools implemented
- **Phase 5 Task 5.4 (transfer smoke test) and Task 5.6 (start wiring) CAN run in parallel** — documented above
- **Phase 7 (Claude Desktop smoke) and Phase 8 (lobster.cash skill) CAN run in parallel** — the skill repo is independent of the MCP server
- **Saul's voice pass on content (Phase 3.5) CAN start at H+24** — Content Planner drafts the blog post at H+22, Saul can review while final polish happens

---

## Cut hierarchy if behind schedule

If behind schedule at any gate, drop in this order:

1. **Drop video recording** (Task 7.7) — save 30 min, ship screenshots only
2. **Drop Base EVM support** — Solana only in v0.1, document as "EVM in v0.2"
3. **Drop `crossmint_transfer_token` (Tool 3)** — ship 3 tools instead of 4, document as "transfer in v0.2"
4. **Drop the lobster.cash skill entirely** — ship MCP server only, document skill as "roadmap"
5. **Drop npm publish** — ship as `git clone` install only, no npx entry
6. **LAST RESORT: Drop Tool 4** — ship as a "Crossmint wallets MCP server without x402" and pitch v0.2 as the x402 story

---

## Open questions for PM / Research

1. **Does `npx crossmint-wallets-mcp` work BEFORE we publish to npm?** (Answer: no — need to publish first or use local path in Claude Desktop config. Planned in Phase 7 Task 7.1.)
2. **What's the npm scope permission for `@0xultravioleta/...`?** Saul needs to have npm publish rights for this scope. If not, fall back to unscoped `crossmint-wallets-mcp` if available.
3. **Should the skill repo be private until Fede certifies?** Phase 8 commits to a local git but doesn't push until Phase 6 integration. Keeping it private until submission is acceptable.
4. **Does the lobster.cash `npx skills add` command work with private repos?** Probably not — the skill needs to be public for `npx skills add` to clone it. So we push public at submission time.

---

## Total time budget for Phase 3 (MCP Build Planner's scope)

| Phase | Hours |
|---|---|
| Phase 1: Scaffold | 1h (H+7 → H+8) |
| Phase 2: Tool 4 (delegated to Critical Path Planner) | 4h (H+8 → H+12) |
| Phase 3: Tool 1 create_wallet | 1h (H+12 → H+13) |
| Phase 4: Tool 2 get_balance | 1h (H+13 → H+14) |
| Phase 5: Tool 3 transfer_token | 2h (H+14 → H+16) |
| Phase 6: MCP wiring | 2h (H+16 → H+18) |
| Phase 7: Claude Desktop smoke test | 2h (H+18 → H+20) |
| Phase 8: lobster.cash skill | 2h (H+20 → H+22) |
| **Total** | **15h (H+7 → H+22)** |

**Buffer:** 0 hours. This is the full extent of the build track. Any slippage must be absorbed by the cut hierarchy.

---

## MCP Build & Skill Planner sign-off

This plan covers ~15 hours of execution across 8 phases. Gates at H+8, H+13, H+14, H+16, H+18, H+20, H+22. Hand-off to Content Planner at H+22 for blog + thread + video script + campaign. Hand-off to Integration Planner at H+22 for polish + submission.

All 4 MCP tools and the lobster.cash skill are covered. Both artifacts are functionally complete by H+22 per this plan.
