# Crossmint Wallets Node.js Quickstart

> Fetched from https://docs.crossmint.com/wallets/quickstarts/nodejs on 2026-04-09

## Installation

Install the Crossmint Wallets SDK:

```bash
# Install command referenced but not fully detailed in source
# Likely: npm install @crossmint/wallets-sdk
```

## Core Code Example

```typescript
import {
    CrossmintWallets,
    createCrossmint,
} from "@crossmint/wallets-sdk";

const crossmint = createCrossmint({
    apiKey: "YOUR_SERVER_API_KEY",
});

const wallets = CrossmintWallets.from(crossmint);

const wallet = await wallets.createWallet({
    chain: "base-sepolia",
    recovery: {
        type: "server",
        secret: process.env.SIGNER_SECRET!,
    },
});
```

## Balance Checking

```typescript
const balances = await wallet.balances(["usdc", "usdxm"]);
for (const token of balances.tokens) {
    console.log(`${token.symbol}: ${token.amount}`);
}
```

## Token Transfers

```typescript
const tx = await wallet.send(
    "RECIPIENT_ADDRESS",
    "usdxm",
    "1"
);
```

## Production Requirements

- Developer account
- Production server API key with scopes:
  - `users.create`
  - `users.read`
  - `wallets.read`
  - `wallets.create`
  - `wallets:transactions.create`
  - `wallets:transactions.sign`
  - `wallets:balance.read`
  - `wallets.fund`

## Notes

- Default chain in example: `base-sepolia` (testnet)
- Recovery type: `server` — the server holds a secret for recovery
- No mention of x402, smart wallet CPI, or agentic payment protocols
- No mention of how to sign a raw transaction hash for an external protocol like x402
