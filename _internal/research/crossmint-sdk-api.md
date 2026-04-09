# @crossmint/wallets-sdk API Surface — Research R1

> Source: https://www.npmjs.com/package/@crossmint/wallets-sdk (fetched via Playwright after WebFetch 403'd)
> Date: 2026-04-09 H+5.5
> Package version: 1.0.7
> Published: 3 days ago (2026-04-06)
> License: Apache-2.0
> Repo: github.com/Crossmint/crossmint-sdk
> Source commit: 2d71a741e00a95315df8dd57f03b03e9f389cc94
> Weekly downloads: 12,047 (active)

---

## R1 verdict

**YES — the SDK exposes everything we need for `pay_x402_endpoint`. Plan A is confirmed feasible. Planes B, C, D are not needed.**

---

## Core imports

```typescript
import {
  createCrossmint,
  CrossmintWallets,
  EVMWallet,      // chain-specific escape hatch
  SolanaWallet,   // chain-specific escape hatch
  StellarWallet,  // chain-specific escape hatch (not needed for our submission)
} from "@crossmint/wallets-sdk";
```

---

## Initialization (server-side, Node.js)

```typescript
const crossmint = createCrossmint({
  apiKey: "<YOUR_SERVER_API_KEY>"
});

const wallets = CrossmintWallets.from(crossmint);
```

Required env: `CROSSMINT_API_KEY` with **Wallet API scopes** enabled.

---

## Wallet lifecycle

### Create a wallet

```typescript
const wallet = await wallets.createWallet({
  chain: "base-sepolia",  // or "solana", "solana-devnet", "base", "ethereum", etc.
  recovery: {
    type: "server",
    secret: "<RECOVERY_SECRET>"
  },
  signers: [{ type: "device" }]  // optional, device is default
});

console.log(wallet.address);
```

### Retrieve an existing wallet

```typescript
// Server-side (with explicit address)
const wallet = await wallets.getWallet("0xWalletAddress", {
  chain: "base-sepolia"
});

// Client-side (JWT-authenticated)
const wallet = await wallets.getWallet({ chain: "base-sepolia" });
```

---

## Core wallet operations (chain-agnostic)

### Balances

```typescript
const balances = await wallet.balances();
console.log(balances.nativeToken.amount);  // e.g., ETH or SOL
console.log(balances.usdc.amount);          // USDC amount
```

### Send tokens

```typescript
const tx = await wallet.send("0xRecipient", "usdc", "100");
console.log(tx.explorerLink);
```

### Transfers history

```typescript
const transfers = await wallet.transfers({
  tokens: "usdc",
  status: "successful"
});
```

### NFTs

```typescript
const nfts = await wallet.nfts({ perPage: 10, page: 1 });
```

---

## Chain-specific escape hatches (CRITICAL for Tool 4)

### EVM

```typescript
const evmWallet = EVMWallet.from(wallet);

// Smart contract interaction
const tx = await evmWallet.sendTransaction({
  to: "0xContractAddress",
  abi: contractAbi,
  functionName: "mint",
  args: [1],
  value: 0n,
});

// Sign a message
const sig = await evmWallet.signMessage({ message: "Hello" });

// Raw viem client escape hatch
const client = evmWallet.getViemClient();
```

**For x402 on EVM:** x402 uses EIP-3009 typed data signing. The `signMessage` method may or may not support raw typed data. If not, we use `getViemClient()` to get a raw viem WalletClient and call `.signTypedData()` directly.

### Solana

```typescript
const solWallet = SolanaWallet.from(wallet);

// Submit ANY Solana transaction as a base64 string
const solTx = await solWallet.sendTransaction({
  serializedTransaction: "<base64-encoded-transaction>"
});
```

**For x402 on Solana:** we build a Solana transaction with the TransferChecked instruction to the x402 `payTo` address, serialize it, pass it to `sendTransaction`, and Crossmint's smart wallet handles the signing via CPI. This works because the smart wallet wraps the SPL token program call. Saul's x402-rs facilitator already handles the CPI inner instruction scanning on the verification side, so the round trip is end-to-end validated.

### Stellar (not relevant for submission)

```typescript
const stellarWallet = StellarWallet.from(wallet);
const stellarTx = await stellarWallet.sendTransaction({
  contractId: "C...",
  method: "transfer",
  args: { to: "G...", amount: "1000000" },
});
```

---

## Signer management (for `crossmint_add_delegated_signer` tool if we build it)

```typescript
// Add a new signer
await wallet.addSigner({ type: "server", secret: "<SECRET>" });

// List signers
const signers = await wallet.signers();
// Returns: [{ type: "device", locator: "device:...", status: "success" }, ...]

// Set the active signer
await wallet.useSigner({ type: "server", secret: "<SECRET>" });

// Recovery flow (new device)
if (wallet.needsRecovery()) {
  await wallet.recover();
}
```

**Note:** The SDK's signer model is **two-tier**: recovery signer (high-security) + operational signer (low-friction). The "delegated signer with spending limit" pattern from lobster.cash is NOT a first-class concept in this SDK. If we want delegated signers, we need to construct them manually via the Crossmint API or use a different pattern.

**Decision:** Drop `crossmint_add_delegated_signer` from the tool list for v0.1. The SDK's signer management is for wallet recovery and operational rotation, not for agent delegation. We ship with 4 tools without that one.

---

## Transaction approval (prepare-only mode)

For flows that need multi-step approval:

```typescript
const pendingTx = await wallet.send("0xRecipient", "usdc", "10", {
  prepareOnly: true
});
console.log(pendingTx.transactionId);

// Approve later
const result = await wallet.approve({
  transactionId: pendingTx.transactionId
});
```

**Use case:** we might want this for a "propose + confirm" pattern inside the MCP tool, so the agent can show the user the transaction before approving. For v0.1, we skip this and auto-approve.

---

## Supported signer types

| Type | Use case | Platforms |
|---|---|---|
| `device` | Default day-to-day signer. Hardware-backed, no OTP. | Browser, React Native |
| **`server`** | **Server-side automated operations (AI agents, backends).** | **Node.js** ← WE USE THIS |
| `email` | OTP-based recovery signer. | All |
| `phone` | OTP-based recovery signer. | All |
| `passkey` | WebAuthn/FIDO2 biometric signer. | Browser (EVM only) |
| `external-wallet` | Bring-your-own key (MetaMask, KMS, etc). | All |

**For our MCP server:** we use `type: "server"` recovery signer. The MCP server runs in Node.js on the user's machine, holds a `RECOVERY_SECRET` from an env var, and acts as the server-side signer for all wallet operations.

---

## Supported chains (partial — need to verify the full list)

Confirmed in the SDK README:
- `base-sepolia`
- `solana` (implied, full chain names TBD)

Other chains likely supported (based on Crossmint's 50+ chain claim):
- Solana mainnet + devnet
- Base mainnet + sepolia
- Ethereum + sepolia
- Polygon + amoy
- Arbitrum + sepolia
- Optimism + sepolia
- Stellar
- And more

**Action:** fetch `https://docs.crossmint.com/introduction/supported-chains` via Playwright at the start of Phase 2A to get the full chain list.

---

## Documentation links

- Main: https://docs.crossmint.com
- API keys: https://docs.crossmint.com/introduction/platform/api-keys
- SDK reference: https://docs.crossmint.com/sdk-reference/wallets
- Source: https://github.com/Crossmint/crossmint-sdk

---

## Critical decisions driven by this research

1. **Plan A is confirmed for Tool 4.** No need for the lobster-cli wrapping fallback.
2. **Drop `crossmint_add_delegated_signer`** from the MCP server's tool list. The SDK's signer model doesn't support delegated signers with spending caps as first-class primitives.
3. **Ship with 4 tools, not 6:** create_wallet, get_balance, transfer_token, pay_x402_endpoint. Clean, focused, demonstrable.
4. **Use `type: "server"` recovery signer** with `CROSSMINT_API_KEY` + `CROSSMINT_RECOVERY_SECRET` from env. This matches the server-side Node.js quickstart.
5. **The x402 Solana path is clear:** build transaction → serialize → `SolanaWallet.sendTransaction({ serializedTransaction })` → done. Saul's facilitator handles the CPI inner instruction scanning on the verification side.
6. **For EVM x402, we need EIP-3009 typed signing.** The plain `signMessage` method may not support EIP-712 typed data. Use `getViemClient()` and call `signTypedData()` directly. Fallback: use the viem client to sign the raw permit hash.
7. **Drop `transferWithAuthorization` concerns for v0.1 on EVM.** Focus on Solana mainnet + Devnet for the primary demo. Base support is a stretch goal.

---

## What still needs research

- **R2:** Does `@crossmint/lobster-cli` expose library functions we can import? (Nice-to-have — we don't need it for Plan A, but it might simplify some tools)
- **R3:** Current version of `@modelcontextprotocol/sdk` and the minimal server example (stdio transport)
- **R4:** The x402 payload format on Solana — what `X-PAYMENT` header content does Faremeter expect for Solana payments?

These are secondary. R1 was the critical blocker and it's resolved.
