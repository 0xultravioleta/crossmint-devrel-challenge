# Hello x402

> The 5-minute quickstart for paying from a Crossmint smart wallet via the x402 HTTP protocol. This is the page `docs.crossmint.com` should have but doesn't — yet.

---

## What this does

In about 90 seconds of runtime (plus one time you copy an API key), this repo will:

1. Create a **Crossmint smart wallet** on Solana Devnet for a given email
2. Send an **x402 payment** in Devnet USDC from that wallet to a paywalled HTTP endpoint
3. Receive the response you paid for and print it, along with the transaction signature

If you can curl an API, you can pay for one with x402.

## Why this exists

Crossmint's x402 support is real: it ships in the [`crossmint-agentic-finance`](https://github.com/Crossmint/crossmint-agentic-finance) monorepo, inside an n8n node, and in production paywalls running on Base and Solana today. But as of April 2026, searching `site:docs.crossmint.com x402` on Google returns exactly one dedicated reference — an n8n node type. There is no standalone quickstart for "I have a Crossmint wallet, how do I send one x402 payment?"

This is that quickstart.

## The opinion

Crossmint's x402 story lives in an 11-demo monorepo and an n8n node reference. A developer landing on `docs.crossmint.com` looking for "my first x402 payment" finds nothing. This repo is the missing page of the docs, shaped as working code.

## Prerequisites

- **Node.js 20 or later** — check with `node --version`
- **pnpm** — install with `npm install -g pnpm` if you don't have it
- **A Crossmint API key** — create a server API key at [crossmint.com/console](https://www.crossmint.com/console). For the quickstart you need these scopes:
  - `wallets.create`
  - `wallets.read`
  - `wallets:transactions.create`
  - `wallets:transactions.sign`
  - `wallets:balance.read`
- **About 2 Devnet USDC** — get them from the [Circle USDC faucet](https://faucet.circle.com) on Solana Devnet. The wallet address is printed the first time you run the script.

That's it. No Docker. No local facilitator. No keypair generation.

## Quickstart

### 1. Clone and install

```bash
git clone https://github.com/<TBD>/hello-x402.git
cd hello-x402
pnpm install
```

### 2. Set your API key

```bash
cp .env.example .env
```

Open `.env` and paste your Crossmint server API key on the `CROSSMINT_API_KEY=` line. Everything else has sane defaults.

### 3. Run it

```bash
pnpm start
```

On the first run, the script creates the wallet and stops to ask you to fund it. Copy the address it prints, go to [faucet.circle.com](https://faucet.circle.com), select **Solana Devnet**, paste the address, and request USDC. Then run `pnpm start` again.

### 4. Read the output

```
Creating Crossmint smart wallet for demo@example.com on solana-devnet...
Wallet address: <address>
Balance: 2.00 USDC

Calling paywalled endpoint: https://<endpoint>/weather
Got HTTP 402 Payment Required
Payment requirements:
  amount: 0.001 USDC
  chain: solana-devnet
  payTo: <address>

Signing payment from Crossmint smart wallet...
Sending payment via Faremeter facilitator (facilitator.corbits.dev)...
Payment confirmed: <tx signature>

Retrying request with X-PAYMENT header...
Got HTTP 200 OK

Response:
{
  "location": "Miami",
  "forecast": "Sunny, 82F",
  "paidFor": "0.001 USDC"
}

Solana Explorer: https://explorer.solana.com/tx/<tx signature>?cluster=devnet
```

## What just happened

Three things worth understanding.

### 1. You created a smart wallet, not an EOA

Crossmint wallets are smart contracts on Solana, not externally owned accounts. That gives you gas sponsorship (Crossmint paid the Solana transaction fee for this payment, not your USDC balance), key rotation without changing addresses, and the ability to grant scoped permissions to agents — all without you writing any Solana program code.

### 2. The x402 payment is just an HTTP header

The paywalled endpoint returns `HTTP 402 Payment Required` with a JSON body describing what it costs and where to pay. Your client constructs a signed payment payload, adds it to the `X-PAYMENT` header, and retries the request. The facilitator verifies the payment on-chain, the server sees the verification, and the response you paid for comes back with an `X-PAYMENT-RESPONSE` header containing the transaction details.

No account setup. No API keys on the merchant side. No Stripe. Just HTTP and USDC.

### 3. On Solana, smart wallet payments are hiding inside inner instructions

This is the detail that breaks naive x402 facilitators. When a Crossmint smart wallet sends USDC, the transfer is executed via **Cross-Program Invocation (CPI)** from the smart wallet program to the SPL Token program. The USDC transfer is NOT a top-level instruction on the transaction — it's nested inside the smart wallet's execution.

Facilitators that only scan top-level instructions will see the smart wallet's authorization instruction but miss the actual USDC movement. They will return "no transfer found" and reject the payment, even though the money moved.

Faremeter and a handful of other facilitators handle this correctly by simulating the transaction and scanning the inner instructions for the `TransferChecked` call. That's why this quickstart uses Faremeter, and why you can swap in other facilitators only if they implement the same CPI inner instruction scanning.

## Expected runtime

| Step | Time |
|---|---|
| `pnpm install` | ~60 seconds |
| First `pnpm start` (wallet creation) | ~5 seconds |
| USDC funding (manual) | 1-2 minutes |
| Second `pnpm start` (payment) | ~10 seconds |
| **Total (first-time)** | **~4 minutes** |
| **Total (API key + funded wallet ready)** | **~90 seconds** |

## Troubleshooting

### `CROSSMINT_API_KEY is missing`

You didn't copy `.env.example` to `.env`, or you didn't paste your key into it. Check with `cat .env` on macOS/Linux or `type .env` on Windows.

### `Wallet balance is 0.00 USDC`

You need to fund the wallet before the payment step. The script prints the wallet address on first run. Copy it, go to [faucet.circle.com](https://faucet.circle.com), select **Solana Devnet**, and request USDC. It arrives in under a minute.

### `Payment rejected: no transfer found in transaction`

You're using a facilitator that doesn't scan CPI inner instructions. Crossmint smart wallets on Solana execute token transfers via CPI, not as top-level instructions. Use Faremeter (the default in this repo) or another facilitator that implements CPI inner instruction scanning.

### `Invalid API key scopes`

Your API key needs: `wallets.create`, `wallets.read`, `wallets:transactions.create`, `wallets:transactions.sign`, `wallets:balance.read`. Regenerate on [crossmint.com/console](https://www.crossmint.com/console) with those scopes checked.

## What's next

- **If you're a Crossmint developer** and you want this page in the docs: the repo is MIT licensed and the blog post in [`content/blog-post.md`](../content/blog-post.md) can be published as-is. A one-line PR on `crossmint-agentic-finance` to link here would also land.
- **If you're an agentic AI builder**: the same pattern works for any AI agent that needs to pay for an API call. Swap the `demo@example.com` email for your agent identity, grant the agent a delegated signer with a spending limit, and you have a payment primitive your agent can use without you holding its keys.
- **If you hit the CPI inner instruction issue** on your own facilitator: look at the `find_transfer_in_inner_instructions` pattern in any of the facilitators that support Crossmint smart wallets. It's a two-path verification: top-level first, simulation + inner instruction scan second.

## Facilitator notes

This quickstart uses **Faremeter** (`facilitator.corbits.dev`) as the default facilitator, because it's the one Crossmint has been focused on during the agentic commerce push.

The demo also works with other public x402 facilitators that implement CPI inner instruction scanning for Solana smart wallets. As of April 2026, the Ultravioleta DAO facilitator at `facilitator.ultravioletadao.xyz` is the only other public facilitator known to support this pattern across Solana plus 18 other mainnets — if you want to test against a second provider, change one environment variable.

## License

MIT. Do whatever you want, including publishing it on Crossmint's blog.
