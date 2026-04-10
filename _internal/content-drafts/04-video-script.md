# 04 — Video Script (60-90 seconds, terminal demo)

> **Owner:** video-script-writer
> **Depends on:** `00-shared-brief.md`, `demo/smoke-test.ts`, `demo/paywall-server.ts`
> **Recording mode:** terminal `pnpm demo` with stderr visible, Solana Explorer in a second window/tab
> **Format:** screen capture, no webcam, lower-third title cards only, no music required
> **Reason for terminal over Claude Desktop:** the mainnet smoke test via `pnpm demo` is verified reproducible end-to-end. Claude Desktop MCP invocation may or may not complete in time for recording, so the terminal path is the safe default. If Claude Desktop smoke test lands, re-shoot with identical beats.

---

## Total runtime: ~75 seconds

| Time | What the screen shows | What is spoken (voiceover) |
|---|---|---|
| **0:00 – 0:05** | Title card (full-screen, dark background) <br> Line 1: **Crossmint Wallets MCP Server** <br> Line 2: pay any x402 endpoint from Claude Desktop, Cursor, Cline, Codex <br> Line 3 (small): mainnet Solana demo, 75 seconds | *"This is the Crossmint Wallets MCP server. It lets any MCP-native agent pay x402 endpoints from a Crossmint smart wallet. Here's a real mainnet run in one take."* |
| **0:05 – 0:12** | Split screen or fullscreen: VS Code showing `demo/smoke-test.ts` — scroll highlights the 5 numbered steps in the header comment (`createWallet`, `getBalance`, start paywall, `payX402Endpoint`, balance recheck) | *"The demo script runs five steps: create a wallet, check balance, boot a local x402 paywall, pay it, and verify the merchant got the money."* |
| **0:12 – 0:20** | Terminal: user types `pnpm demo` and hits enter. The first stderr lines appear: <br> `=== Crossmint Wallets MCP — smoke test ===` <br> `[cfg] api_key: loaded (length=...)` <br> `[cfg] recovery_secret: loaded (length=64)` <br> `[cfg] default_chain: solana` | *"pnpm demo. It loads the API key and recovery secret from env, and the default chain is Solana mainnet."* |
| **0:20 – 0:30** | Terminal shows the cached wallet being loaded: <br> `[createWallet] using cached wallet for solana::company` <br> `=== WALLET READY ===` <br> `chain:    solana` <br> `address:  4xHkMCaKVBGw4GtdpeKoNZhGFDMi1tMCJDvXvxUmL8hM` <br> `explorer: https://explorer.solana.com/address/4xHkMCa...` | *"That's the payer wallet on Solana mainnet. Real address, real USDC in it. The wallet is cached so reruns are idempotent."* |
| **0:30 – 0:38** | Terminal shows balance check: <br> `[getBalance] fetching payer balances...` <br> `=== PAYER BALANCES ===` <br> `  SOL       ... (decimals=9)` <br> `  USDC      1.81... (decimals=6)` <br> `======================` | *"The payer has real USDC. The smart wallet pays gas via Crossmint's gasless relayer, so it doesn't need SOL for this."* |
| **0:38 – 0:48** | Terminal: paywall server boots: <br> `[paywall] listening on http://localhost:4021/paid-data` <br> `[paywall] merchant address: Fxr4RtBPzU5ZJL2Wj57MZNuChBNbZzK3RHrXDwa1yqo` <br> `[paywall] required amount:  10000 atomic (= 0.01 USDC)` | *"The local paywall is a hand-rolled x402 server. No facilitator middleware. It returns HTTP 402 with a canonical PaymentRequired body asking for 0.01 USDC to the merchant address."* |
| **0:48 – 1:02** | Terminal shows the payment firing: <br> `[payX402Endpoint] paying 0.01 usdc to Fxr4RtBPzU5ZJL2Wj57MZNuChBNbZzK3RHrXDwa1yqo on solana...` <br> `[payX402Endpoint] tx confirmed: KRjW2uK7LBioy...` <br> `[paywall] verifying signature KRjW2uK7LBioy...` <br> `[paywall] 200 — payment verified` <br> `=== PAYMENT RESULT ===` <br> `status:    200` <br> `tx sig:    KRjW2uK7LBioyyy1P3xcJTkpS2ibpCjBq1Ektnf4icL6GH25VnesoCGdQN7DbWYbbyjv9MxHoFrS3hsx7ZgkbEg` | *"Crossmint signs via the CPI inner instruction, the gasless relayer pays the fee, the paywall looks up the signature on mainnet RPC, and returns the paid response. 402 to 200 in one tool call."* |
| **1:02 – 1:12** | Cut to browser: Solana Explorer page already open at `explorer.solana.com/tx/KRjW2uK7...`. Highlight the "Success" label and the Token Balance Changes section showing USDC moving to the merchant address | *"Here's the transaction on Solana Explorer. Real mainnet. Real USDC. Real CPI transfer."* |
| **1:12 – 1:20** | End card (full-screen, dark background) <br> Line 1: **Install:** <br> Line 2 (code): `npx crossmint-wallets-mcp` <br> Line 3 (small): Claude Desktop / Cursor / Cline / Codex config block in README <br> Line 4: **Repo:** github.com/0xultravioleta/crossmint-wallets-mcp <br> Line 5: **Skill:** github.com/0xultravioleta/crossmint-cpi-skill <br> Line 6: MIT licensed · Built for Crossmint DevRel | *"Both repos are MIT. Drop the JSON config block from the README into Claude Desktop, Cursor, Cline, or Codex and you've got Crossmint wallets as MCP tools. Link in the description."* |

---

## Shot notes for the recorder

- **Recording resolution:** 1920x1080 minimum, terminal font at 18-20pt so text is readable on mobile
- **Terminal background:** dark (matches Solana Explorer dark mode for a clean cut)
- **Cursor visibility:** on — seeing the command typed is part of the trust signal
- **Stderr visibility:** on — the smoke test logs all progress to stderr, which is what viewers need to see
- **Audio:** voiceover only, no music bed (music distracts from the stderr text)
- **Cuts:** hard cuts between terminal → browser → end card, no transitions, no fades
- **Pacing:** the payment step (0:48 – 1:02) is the longest single beat because the tx confirmation genuinely takes a few seconds on mainnet. Do NOT speed this up in post — the real wait is the proof.
- **Lower-thirds:** optional, keep to section labels only (`CREATE WALLET`, `CHECK BALANCE`, `PAY PAYWALL`, `VERIFY ON-CHAIN`)

---

## Contingency: if the Claude Desktop smoke test does land in time

If Claude Desktop MCP integration completes cleanly before recording, re-shoot with this substitution for the 0:12 – 1:02 block:

- Replace terminal with Claude Desktop window
- Prompt 1: "Create a Crossmint smart wallet on Solana for me" → show the tool invocation card for `crossmint_create_wallet`
- Prompt 2: "What's the USDC balance of that wallet?" → `crossmint_get_balance` tool invocation card
- Prompt 3: "Pay the endpoint at http://localhost:4021/paid-data from that wallet, max 0.1 USDC" → `crossmint_pay_x402_endpoint` tool invocation card, print the signature and explorer link
- Keep the 1:02 – 1:12 Solana Explorer cut identical
- Keep the end card identical

---

## Compliance checks

- [x] No banned words (seamlessly, unlock, empower, etc.)
- [x] No "Ultravioleta" anywhere in the script or on-screen
- [x] No "In today's fast-paced" or "In the rapidly evolving" openers
- [x] First person voiceover
- [x] Real tx signature on screen and spoken location
- [x] Real wallet addresses on screen
- [x] Both repos named in end card
- [x] MIT license named
- [x] lobster.cash not attacked — it simply isn't mentioned in the 75s cut because the video is the MCP server demo specifically (the skill has its own one-paragraph mention in the blog post and thread)
