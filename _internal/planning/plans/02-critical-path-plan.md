# 02 — Critical Path Plan (Tool 4: pay_x402_endpoint)

> **Role:** Critical Path (Tool 4) Planner
> **Owner:** Claude (direct execution)
> **Mandate:** Plan the build of `crossmint_pay_x402_endpoint` — the highest-risk single deliverable
> **Scope:** Phase 2C (H+8 → H+12) + all 4 fallback plans A/B/C/D with hour-resolution pivot triggers
> **Depends on:** `01-research-plan.md` (R1 verdict: Plan A confirmed)
> **Line target:** 400-600

---

## Framing discipline

- **Faremeter first**, Ultravioleta facilitator is NOT named in the MCP server code or README
- No `0x` private keys in any file committed to git
- The subject of this tool is "pay an x402 endpoint from a Crossmint smart wallet" — the facilitator is infrastructure, not showcase
- Solana is the primary demo chain (per Solana Foundation's publicly stated gap)
- Base EVM is a stretch goal; Stellar is out of scope

---

## What this plan is NOT

- NOT the plan for the other 3 MCP tools (`create_wallet`, `get_balance`, `transfer_token`) — those belong to `03-mcp-build-and-skill-plan.md`
- NOT the plan for MCP server wiring (tools/list, tools/call JSON-RPC handlers) — that's also `03`
- NOT the plan for the lobster.cash skill (Artifact 2) — that's `03`
- NOT the plan for the blog post or campaign proposal — that's `04`
- NOT the plan for polish, QA, or submission — that's `05`
- NOT the execution itself — this document is ONLY the plan; execution produces the actual `src/core/pay-x402-endpoint.ts` file
- NOT a plan to modify Crossmint's SDK or Faremeter's facilitator

---

## Dependencies on other plans

- **Depends on:** `01-research-plan.md` (Phase 1 verdict: Plan A feasible)
- **Provides to:** `03-mcp-build-and-skill-plan.md` — the MCP Build Planner integrates Tool 4 into the MCP server stubs

---

## Context summary (from research files)

Confirmed from `_internal/research/crossmint-sdk-api.md`:

```typescript
import { SolanaWallet } from "@crossmint/wallets-sdk";

const solWallet = SolanaWallet.from(wallet);
const solTx = await solWallet.sendTransaction({
  serializedTransaction: "<base64-encoded-transaction>"
});
```

This is the primitive. Build a Solana transaction client-side, serialize it to base64, pass it to `sendTransaction`, Crossmint's smart wallet signs via CPI. End result: a confirmed Solana transaction that transferred USDC to the x402 `payTo` address.

For EVM:
```typescript
const evmWallet = EVMWallet.from(wallet);
const viemClient = evmWallet.getViemClient();
// Use viem's signTypedData for EIP-712 / EIP-3009 typed data signing
```

---

## Phase 1 — Target endpoint selection and pre-build setup (H+8:00 → H+8:30)

### Task 1.1 — Identify target x402 endpoint

- **Action:** Pick ONE target endpoint for the Phase 2C build. Preference order:
  1. **Faremeter public test endpoint** if reachable at `facilitator.corbits.dev` or similar — the cleanest option
  2. **`crossmint-agentic-finance/weather`** subfolder — clone locally, run as Express server, target `http://localhost:3000/weather`
  3. **`crossmint-agentic-finance/ping-faremeter-crossmint-solana`** — if already has a paywalled endpoint configured
- **Command sequence:**
  ```bash
  curl -i https://facilitator.corbits.dev/health  # check if Faremeter public endpoint exists
  # If no public endpoint, clone + run:
  git clone https://github.com/Crossmint/crossmint-agentic-finance /tmp/caf
  cd /tmp/caf/weather && npm install && npm start &
  curl -i http://localhost:3000/weather  # confirm 402 response
  ```
- **Output:** pick endpoint URL, record in `_internal/research/x402-payload-format.md` and in `src/core/pay-x402-endpoint.ts` as a test constant
- **Time budget:** 20 minutes
- **Fallback:** If none of the 3 options are reachable, write a 40-line minimal Express paywall server inside `z:\crossmint-wallets-mcp\demo\paywall-server.ts` that returns a valid x402 402 response. This is a last resort but keeps the demo self-contained.

### Task 1.2 — Inspect the 402 response body structure

- **Action:** `curl -i <chosen-endpoint>` and document the 402 response fields
- **Expected fields** (per x402 spec):
  ```json
  {
    "x402Version": 1,
    "error": "Payment required",
    "accepts": [
      {
        "scheme": "exact",
        "network": "solana-devnet",
        "maxAmountRequired": "1000",
        "resource": "http://localhost:3000/weather",
        "description": "Weather data",
        "mimeType": "application/json",
        "payTo": "<solana-address>",
        "maxTimeoutSeconds": 60,
        "asset": "<usdc-mint-address>",
        "extra": {}
      }
    ]
  }
  ```
- **Output:** save raw 402 response to `_internal/research/x402-payload-format.md`
- **Time budget:** 10 minutes
- **Fallback:** Read the x402 spec types at `github.com/coinbase/x402/tree/main/packages/types/src/index.ts` or Saul's own `z:\ultravioleta\dao\x402-rs\src\types.rs` for the authoritative structure

### Task 1.3 — Create the Crossmint smart wallet (Solana Devnet)

- **Action:** Use the Crossmint API key stored in `.env` to create a test wallet via `@crossmint/wallets-sdk`
- **Code:**
  ```typescript
  import { createCrossmint, CrossmintWallets } from "@crossmint/wallets-sdk";

  const crossmint = createCrossmint({ apiKey: process.env.CROSSMINT_API_KEY! });
  const wallets = CrossmintWallets.from(crossmint);
  const wallet = await wallets.createWallet({
    chain: "solana-devnet",
    recovery: { type: "server", secret: process.env.CROSSMINT_RECOVERY_SECRET! }
  });
  console.log("Test wallet address:", wallet.address);
  ```
- **Output:** wallet address logged to stderr, saved in `demo/test-wallet.txt` (gitignored)
- **Time budget:** 15 minutes
- **Fallback:** If `chain: "solana-devnet"` is not supported, try `chain: "solana"` with the `network: "devnet"` option. If that fails, check the Crossmint console UI to see what Solana chain IDs exist.

### Task 1.4 — Fund the wallet with Devnet USDC

- **Action:** Go to Circle USDC Devnet faucet at `https://faucet.circle.com` → select Solana Devnet → paste wallet address → request 2 USDC
- **Alternative:** Use `solana airdrop 2 <address> --url devnet` for native SOL, then swap for USDC (but this is more work)
- **Verification:** Call `wallet.balances()` and confirm USDC balance > 0
- **Time budget:** 5 minutes
- **Fallback:** If Circle faucet is rate-limited or down, try alternative faucets like `solana-faucet.com` or `faucet.solana.com`. If no faucet works, use a personal Devnet wallet to transfer USDC directly to the test wallet.

### Phase 1 gate (H+8:30)

- **Criteria:**
  - [ ] Target endpoint returns a 402 with valid structure
  - [ ] Crossmint smart wallet created on Solana Devnet
  - [ ] Wallet funded with at least 1 USDC on Devnet
- **Pass action:** Proceed to Phase 2
- **Fail action:** If endpoint unreachable → write local paywall (extra 30 min). If wallet creation fails → file blocker in `pm-journal.md`, consider Plan D fallback immediately.

---

## Phase 2 — Plan A execution: direct SDK signing (H+8:30 → H+10:30)

### Task 2.1 — Build the Solana transaction client-side

- **Action:** Construct a `TransferChecked` SPL token instruction targeting the x402 `payTo` address for the `maxAmountRequired` amount of USDC
- **Dependencies:** Add `@solana/web3.js` and `@solana/spl-token` to `package.json`
- **Code pattern:**
  ```typescript
  import { Connection, PublicKey, Transaction } from "@solana/web3.js";
  import {
    createTransferCheckedInstruction,
    getAssociatedTokenAddress,
    TOKEN_PROGRAM_ID,
  } from "@solana/spl-token";

  async function buildX402PaymentTx(
    fromWalletAddress: string,
    payTo: string,
    usdcMint: string,
    amount: bigint,
    rpcUrl: string
  ): Promise<string> {
    const connection = new Connection(rpcUrl, "confirmed");
    const fromPubkey = new PublicKey(fromWalletAddress);
    const toPubkey = new PublicKey(payTo);
    const mintPubkey = new PublicKey(usdcMint);

    const fromAta = await getAssociatedTokenAddress(mintPubkey, fromPubkey, true);
    const toAta = await getAssociatedTokenAddress(mintPubkey, toPubkey, true);

    const instruction = createTransferCheckedInstruction(
      fromAta,
      mintPubkey,
      toAta,
      fromPubkey,  // authority = Crossmint smart wallet (CPI'd)
      amount,
      6,  // USDC decimals
      [],
      TOKEN_PROGRAM_ID
    );

    const { blockhash } = await connection.getLatestBlockhash();
    const tx = new Transaction();
    tx.recentBlockhash = blockhash;
    tx.feePayer = fromPubkey;
    tx.add(instruction);

    return tx.serialize({ requireAllSignatures: false, verifySignatures: false }).toString("base64");
  }
  ```
- **Output:** base64-encoded transaction string ready to pass to `SolanaWallet.sendTransaction`
- **Time budget:** 45 minutes
- **Fallback:** If the associated token account doesn't exist on the `toPubkey` side, add `createAssociatedTokenAccountInstruction` before the transfer (Crossmint's wallet pays gas so this is fine)

### Task 2.2 — Submit via `SolanaWallet.sendTransaction`

- **Action:** Call the Crossmint SDK to submit the transaction
- **Code:**
  ```typescript
  import { SolanaWallet } from "@crossmint/wallets-sdk";

  const solWallet = SolanaWallet.from(wallet);
  const result = await solWallet.sendTransaction({
    serializedTransaction: base64Tx
  });
  console.error("Tx signature:", result.hash);  // stderr, not stdout
  return result;
  ```
- **Output:** transaction signature (Solana)
- **Time budget:** 20 minutes
- **Fallback:** If the SDK throws "unsupported transaction type" or similar, use the `sendTransaction` method with a different serialization format. If truly stuck, trigger hour-1 pivot to Plan B.

### Task 2.3 — Wait for confirmation

- **Action:** Poll Solana RPC for transaction confirmation (confirmed or finalized status)
- **Code:**
  ```typescript
  const status = await connection.confirmTransaction(result.hash, "confirmed");
  if (status.value.err) throw new Error("tx failed: " + JSON.stringify(status.value.err));
  ```
- **Time budget:** 10 minutes (including retry logic)
- **Fallback:** If confirmation times out after 60 seconds, the tx may still succeed — retry the x402 endpoint anyway with the sig in the header

### Task 2.4 — Construct X-PAYMENT header and retry the HTTP request

- **Action:** Build the `X-PAYMENT` header value per x402 spec and retry the paywalled request
- **Format** (per x402 spec):
  ```
  X-PAYMENT: <base64(JSON({
    x402Version: 1,
    scheme: "exact",
    network: "solana-devnet",
    payload: {
      signature: <tx-signature>,
      transaction: <base64-serialized-signed-tx>,  // or just signature, depending on scheme
    }
  }))>
  ```
- **Code:**
  ```typescript
  const paymentHeader = Buffer.from(JSON.stringify({
    x402Version: 1,
    scheme: "exact",
    network: "solana-devnet",
    payload: {
      signature: result.hash,
      // ... additional fields per chosen scheme
    }
  })).toString("base64");

  const response = await fetch(endpoint, {
    headers: { "X-PAYMENT": paymentHeader }
  });
  if (!response.ok) throw new Error(`Paid but got ${response.status}`);
  return await response.json();
  ```
- **Output:** the successful HTTP 200 response body
- **Time budget:** 25 minutes
- **Fallback:** If the endpoint rejects with "invalid payment scheme", inspect the exact `scheme` field in the original 402 `accepts[]` array and match it. Different schemes may require different payload shapes.

### Task 2.5 — Smoke test + commit

- **Action:** Run the full flow end-to-end from a clean state
- **Command:**
  ```bash
  cd z:/crossmint-wallets-mcp
  pnpm tsx demo/smoke-test.ts
  # Expected output: wallet created -> USDC balance OK -> 402 received -> tx sent -> 200 response -> JSON body printed
  ```
- **Commit:** `feat(core): implement pay_x402_endpoint via direct SDK signing on Solana`
- **Time budget:** 20 minutes (including debugging)
- **Fallback:** If smoke test fails for a reason not anticipated above, trigger hour-2 pivot decision

### Hour-1 pivot trigger (H+9:30)

- **Check:** By H+9:30 (1 hour into Phase 2), is Task 2.1 (transaction construction) complete and the transaction successfully serialized?
- **If YES:** Continue with Plan A, move to Task 2.2
- **If NO:** The client-side transaction construction is unexpectedly hard. Do one of:
  - If stuck on Solana primitives (ATA, mint, decimals): continue for 30 more minutes, then pivot to Plan D (direct transfer fallback)
  - If stuck on the Crossmint SDK's `SolanaWallet` not accepting the format: pivot to Plan B immediately

### Phase 2 gate (H+10:30)

- **Criteria:**
  - [ ] `src/core/pay-x402-endpoint.ts` exists and passes `tsc --noEmit`
  - [ ] Smoke test in `demo/smoke-test.ts` prints an HTTP 200 response body
  - [ ] Transaction signature is visible on Solana Devnet explorer
- **Pass action:** Tool 4 Plan A is a success. Proceed to MCP wiring in Phase 2E (owned by MCP Build Planner).
- **Fail action:** Hour-2 pivot to Plan B.

---

## Phase 3 — Plan B mitigation: wrap `@crossmint/lobster-cli` (H+10:30 → H+12:00 if triggered)

### When this phase triggers

Only if Phase 2 Plan A fails to produce a successful payment by H+10:30.

### Task 3.1 — Install `@crossmint/lobster-cli` as a dependency

- **Action:**
  ```bash
  cd z:/crossmint-wallets-mcp
  pnpm add @crossmint/lobster-cli
  ```
- **Time budget:** 5 minutes

### Task 3.2 — Use library exports to create wallet + pay

- **Action:** Use the exported functions from `@crossmint/lobster-cli` to handle the wallet creation and x402 fetch
- **Code pattern:**
  ```typescript
  import {
    runSetupFlow,
    withAuthenticatedApi,
    createTransaction,
    approveTransaction,
  } from "@crossmint/lobster-cli";

  // Note: may require spawning the consent flow if agent is not set up
  await runSetupFlow({ ... });

  // Then use the authenticated API to construct and approve the x402 payment
  await withAuthenticatedApi(async (api) => {
    // Call the x402 fetch flow via the library
    // EXACT function name TBD — will be discovered by reading
    // @crossmint/lobster-cli's src/index.ts at runtime
  });
  ```
- **Time budget:** 45 minutes
- **Fallback:** If the library doesn't expose a direct x402 function, fall through to Plan C

### Task 3.3 — Shell out to `lobstercash crypto x402 fetch` as last resort

- **Action:** If library imports don't work cleanly, shell out to the CLI directly
- **Code:**
  ```typescript
  import { execSync } from "child_process";

  const output = execSync(
    `npx @crossmint/lobster-cli crypto x402 fetch ${endpoint}`,
    { encoding: "utf-8" }
  );
  return JSON.parse(output);  // may need parsing — CLI output is human-readable
  ```
- **MCP compatibility risk:** CLI output is human-readable, not JSON. Need to parse or use `--json` flag if available.
- **Time budget:** 20 minutes
- **Fallback:** Document as "degraded mode" in README

### Hour-2 pivot trigger (H+11:30)

- **Check:** By H+11:30, is Plan B returning a successful HTTP 200 from the target endpoint?
- **If YES:** Proceed to smoke test and commit
- **If NO:** Pivot to Plan C immediately (Crossmint REST API direct)

---

## Phase 4 — Plan C mitigation: Crossmint REST API direct (H+11:00 → H+12:00 if triggered)

### When this phase triggers

Only if Plan A and Plan B both fail to produce a successful payment.

### Task 4.1 — Use the Crossmint REST API for wallet creation

- **Action:** Call `POST https://www.crossmint.com/api/2022-06-09/wallets` directly instead of using the SDK
- **Time budget:** 20 minutes
- **Reference:** `https://docs.crossmint.com/api-reference/wallets/create-wallet`

### Task 4.2 — Sign the Solana transaction via Crossmint REST signing endpoint

- **Action:** Use `POST /wallets/{walletLocator}/transactions` with a base64 serialized tx
- **Time budget:** 30 minutes
- **Fallback:** If no raw signing endpoint exists at the REST level (which would be a surprise), fall through to Plan D

### Hour-3 pivot trigger (H+11:45)

- **Check:** By H+11:45, is Plan C returning a successful HTTP 200 from the target endpoint?
- **If YES:** Continue, smoke test, commit
- **If NO:** Pivot to Plan D immediately (degraded transfer fallback)

---

## Phase 5 — Plan D (degraded fallback): transfer-only mode (H+11:45 → H+12:00 if triggered)

### When this phase triggers

Only if Plans A, B, and C all fail. This is the "ship something even if degraded" path.

### Task 5.1 — Use `wallet.send()` as the payment primitive

- **Action:** Call `wallet.send(payTo, "usdc", amount)` — the standard transfer method. Then retry the x402 endpoint with a manually constructed `X-PAYMENT` header referencing the resulting transaction signature.
- **Code:**
  ```typescript
  const tx = await wallet.send(payTo, "usdc", amountHuman);
  const paymentHeader = buildDegradedXPaymentHeader(tx.hash);
  const response = await fetch(endpoint, {
    headers: { "X-PAYMENT": paymentHeader }
  });
  ```
- **Time budget:** 15 minutes

### Task 5.2 — Document the degraded mode in README

- **Action:** Add a mandatory README section titled "v0.1 limitations" that explains:
  - Only "direct transfer" x402 schemes are supported in v0.1
  - Other x402 schemes (authorization, exact, etc.) are NOT supported and will return an error
  - Users who need other schemes should use `@crossmint/lobster-cli crypto x402 fetch` as a fallback
- **File:** `z:\crossmint-wallets-mcp\README.md` (update during Phase 2B scaffold)
- **Time budget:** 10 minutes

### Phase 5 gate

- **Criteria:** At minimum, a smoke test that succeeds for ONE x402 scheme (direct transfer) from a Crossmint Solana smart wallet
- **Pass action:** Ship the submission with a clear caveat section
- **Fail action:** This is catastrophic — the MCP server doesn't ship Tool 4. Fall back to shipping only the lobster.cash skill as Artifact 2, pivot the submission to a skill-only concept. Notify Saul immediately.

---

## Phase 6 — Integration into MCP server (handoff to Planner #3)

### Task 6.1 — Expose `payX402Endpoint` as a core function

- **Action:** Ensure `src/core/pay-x402-endpoint.ts` exports a clean async function with this signature:
  ```typescript
  export async function payX402Endpoint(opts: {
    email: string;
    url: string;
    maxUsdc?: number;
    headers?: Record<string, string>;
    jsonBody?: unknown;
  }): Promise<{
    transactionSignature: string;
    responseStatus: number;
    responseBody: unknown;
    explorerLink: string;
  }>;
  ```
- **Handoff:** MCP Build Planner (`03-mcp-build-and-skill-plan.md`) wraps this in a tool definition
- **Time budget:** 15 minutes (during Phase 2E MCP wiring, NOT during Phase 2C)

---

## Hour-resolution pivot triggers (summary)

| Time | Check | Pass → | Fail → |
|---|---|---|---|
| H+8:30 | Phase 1 gate passed (endpoint + wallet + funding) | Phase 2 Plan A | Emergency: fix blockers OR start Plan D immediately |
| H+9:30 | Task 2.1 complete (tx serialized) | Continue Plan A | Continue 30 min, then Plan D OR Plan B |
| H+10:30 | Phase 2 gate (smoke test green) | Tool 4 DONE, proceed to Phase 2E wiring | Hour-2 pivot: Plan B |
| H+11:30 | Plan B succeeded | Smoke test + commit | Hour-3 pivot: Plan C |
| H+11:45 | Plan C succeeded | Smoke test + commit | Emergency pivot: Plan D |
| H+12:00 | Hard deadline — SOMETHING ships | Tool 4 DONE in whatever plan succeeded | Catastrophic: drop Tool 4, skill-only submission |

---

## Gate criteria for H+12

- [ ] `src/core/pay-x402-endpoint.ts` exists, exports the `payX402Endpoint` function
- [ ] Smoke test in `demo/smoke-test.ts` demonstrates a successful x402 payment from a Crossmint smart wallet on Solana Devnet
- [ ] Transaction signature visible on Solana Devnet explorer
- [ ] Response body from the x402 endpoint is captured and returned
- [ ] Code is committed to the `crossmint-wallets-mcp` repo

---

## Open questions for PM / Research

1. **Is there a public Faremeter test endpoint available, or do we need to run our own paywall server?** Phase 1 Task 1.1 answers this during build; not a planning blocker.
2. **Does the x402 "exact" scheme require the signed transaction bytes in the X-PAYMENT header, or just the resulting tx signature?** Phase 1 Task 1.2 answers this by inspecting the 402 response and x402 spec. Critical for Task 2.4.
3. **What is the exact USDC mint address on Solana Devnet that the Crossmint SDK expects?** Use the canonical `Gh9ZwEmdLJ8DscKNTkTqPbNwLNNBjuSzaG9Vp2KGtKJr` or similar — verify during Task 2.1.

---

## Critical path planner sign-off

Plan A is the happy path. Plans B/C/D are documented mitigations with hour-resolution triggers. If Plan A succeeds (expected per R1), Tool 4 ships by H+10:30 with 90 minutes of buffer before Phase 2D starts. Handoff to MCP Build Planner is clean.
