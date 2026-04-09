# @crossmint/lobster-cli — Research R2

> Source: https://www.npmjs.com/package/@crossmint/lobster-cli (fetched via Playwright)
> Date: 2026-04-09 H+5.75
> Package version: **3.0.8** (published **1 HOUR AGO** — 2026-04-09 afternoon)
> License: MIT
> Repo: **github.com/Crossmint/lobster.cash** (NOT lobstercash-cli-skills — that's a separate skills repo)
> Source entry: `packages/cli/src/index.ts`
> Weekly downloads: 99 (new package, actively maintained)

---

## R2 verdict

**YES — @crossmint/lobster-cli exposes extensive library exports. Two paths are now viable for Tool 4:**
- **Path A (primary):** direct `@crossmint/wallets-sdk` usage for full control
- **Path B (alternative):** import `@crossmint/lobster-cli` library functions

**Decision:** MCP server uses Path A (direct SDK) for clean separation from lobster.cash's config state. The lobster.cash skill (Artifact 2) references the `lobstercash crypto x402 fetch` CLI command directly — that is exactly the abstraction the skill is supposed to delegate to.

**Strategic bonus:** lobster-cli v3.0.8 was published ONE HOUR before we started research. This is actively shipped code from Crossmint RIGHT NOW. Referencing it in the blog post is a fresh signal — we are aligned with current releases, not chasing stale docs.

---

## Library exports (for programmatic use)

```typescript
import {
  parseConfig,
  getOrCreateWallet,
  ensureAuthenticated,
  withAuthenticatedApi,
  getWalletBalance,
  createTransaction,
  approveTransaction,
  runSetupFlow,
} from "@crossmint/lobster-cli";
```

Full list at `github.com/Crossmint/lobster.cash/blob/HEAD/packages/cli/src/index.ts`.

**Observed:** No direct `fetchX402(url)` export. The x402 fetch flow is internal to the CLI command handler. To use it programmatically we would need to call `createTransaction`, `approveTransaction`, and handle the HTTP round trip ourselves — which is exactly what Path A does with `@crossmint/wallets-sdk`, just one level lower.

**Conclusion:** there is no significant benefit to wrapping lobster-cli over using `@crossmint/wallets-sdk` directly. Go Path A.

---

## CLI command surface (for reference)

### Setup and status
- `lobstercash setup` — Generate local keypair, start consent flow
- `lobstercash status` — Check agent status (wallet, balances, cards, payment readiness)
- `lobstercash examples` — Show working examples

### Crypto operations
- `lobstercash crypto balance` — Check wallet balances
- `lobstercash crypto send --to <address> --amount <amount> [--token usdc] [--timeout 60000]` — Send tokens
- `lobstercash crypto deposit --amount <amount> --description "<desc>"` — Generate deposit URL for human approval
- **`lobstercash crypto x402 fetch <url> [--network mainnet-beta] [--json '<body>'] [--header "Key: Value"] [--debug]`** — **Fetch x402-protected URL, automatically handling payment**
- `lobstercash crypto tx create --type transfer|serialized|calls [args]` — Create transaction without approving
- `lobstercash crypto tx approve --id <txId> --message <msg> [--encoding utf8]` — Approve by signing
- `lobstercash crypto tx approve --id <txId> --signature <sig>` — Approve with pre-computed signature
- `lobstercash crypto tx status --id <txId> [--timeout 60000]` — Check tx status

### Virtual cards
- `lobstercash cards request --amount <amount> --description "<description>"` — Request virtual card
- `lobstercash cards list` — List card order intents
- `lobstercash cards reveal --card-id <id> --merchant-name "<name>" --merchant-url "<url>" --merchant-country <XX>` — Reveal card for purchase

### Agent management
- `lobstercash agents register --name <name> [--description <desc>] [--image-url <url>]` — Register new agent
- `lobstercash agents list` — List agents
- `lobstercash agents set-active <agentId>` — Change active agent

### Global options
- `--server <url>` — Default: `https://www.lobster.cash`
- `--timeout <ms>` — Default: 15000ms

---

## Storage

- Default: `~/.lobster/agents.json` (mode 0600)
- Override: `LOBSTER_CASH_WALLETS_DIR` env var
- Auto-migrates from `~/.openclaw/lobster-cash/wallets.json` (legacy)

**Conflict with MCP server:** If we wrapped lobster-cli inside our MCP server, the MCP server would share `~/.lobster/agents.json` state with the user's existing lobster-cli installation. This creates unwanted coupling — installing our MCP server could interfere with the user's existing lobster.cash setup.

**This is another reason to use Path A (direct SDK) for the MCP server.** Our MCP server keeps its own state isolated, operates independently of lobster.cash's config, and lets both products coexist without collision.

---

## Output contract

- stdout: human-readable text
- stderr: errors as plain text
- Exit 0 = success, 1 = failure

**MCP compatibility issue:** MCP servers communicate via JSON-RPC over stdout. A CLI with human-readable stdout output would **conflict with MCP transport** if we shelled out and captured stdout. This is yet another reason to NOT wrap lobster-cli in the MCP server.

---

## Architecture decision recorded

**MCP server (Artifact 1):**
- Direct dependency on `@crossmint/wallets-sdk@^1.0.7` only
- No dependency on `@crossmint/lobster-cli`
- No dependency on `~/.lobster/` state
- Clean Node.js package that ships as `npx crossmint-wallets-mcp`

**lobster.cash skill (Artifact 2):**
- Skill markdown references `lobstercash crypto x402 fetch <url>` as the execution primitive
- Depends on the user having `@crossmint/lobster-cli@^3.0.0` installed (which is the norm for lobster.cash users)
- Does not conflict with MCP server — different transport, different audience
- The skill TEACHES the Solana CPI inner instruction nuance that affects x402 payments from Crossmint smart wallets

**The blog post ties them together:** same gap, same primitive, two entry points — one for MCP-native clients, one for lobster.cash-compatible clients.

---

## What I did not verify

- The exact function signature of `createTransaction` and `approveTransaction` in lobster-cli's library exports (would require reading source)
- Whether `withAuthenticatedApi` takes a callback or returns a client object
- The consent flow UX (only relevant if we shell out from the skill, which we don't need to)

These are OK to leave unanswered because we are not wrapping lobster-cli. If the MCP server's Path A fails, we revisit and read the lobster-cli source.

---

## Research complete

- [x] R1 — @crossmint/wallets-sdk API surface (Plan A confirmed)
- [x] R2 — @crossmint/lobster-cli library exports (Path B available but not needed)
- [ ] R3 — @modelcontextprotocol/sdk minimal server example
- [ ] R4 — x402 payload format on Solana (Faremeter expectations)

Moving to R3.
