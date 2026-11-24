# FLOP Agent Guide — Run a Testnet Agent & Earn Vouchers

A step-by-step installation guide for the **Agent** role on the [FLOP Network](https://flop.finance) testnet: create an identity, enrol with the gateway, claim test FLOP, and spend it on settled inference to earn agent vouchers.

> **Status check first (read this before installing anything)**
> The FLOP testnet chain is live, but **voucher earning has NOT started yet**. Earning begins at a future *activation block* that Flop Labs will announce — activity before that block earns zero and is not backfilled. The public faucet also still serves **devnet** at the time of writing. Install and practise now so you are ready on day one, but don't expect vouchers until activation is announced.
>
> Test FLOP has **no monetary value**. No airdrop is guaranteed. This is an unofficial community guide, not a Flop Labs product.

---

## 1. Pick your role

FLOP block rewards split **75% miners / 10% validators / 10% agents / 5% community stakers**.

| Role | Hardware | How it earns | Barrier |
| --- | --- | --- | --- |
| **Agent** (this guide) | Any computer | Spends test FLOP on settled inference → 1 voucher per FLOP spent; airdrop share = your vouchers ÷ all vouchers | None — open to everyone |
| Miner | NVIDIA GPU, 16 GB+ VRAM | Serves inference sessions | GPU required |
| Validator | Dedicated host, ~8 cores / 32 GB RAM / 2 TB NVMe | Block production + finality (10% of block reward + 15% of inference fees) | Application-based, seats are selected by the FLOP team |

The Agent role is the only one that needs **no GPU, no big server, and no application**. Holding FLOP earns nothing — only *settled inference spend* counts.

## 2. Prerequisites

- Linux or macOS (Windows: use WSL2)
- Node.js 22+ (with `npx`)
- `curl` and `git`
- A **dedicated test wallet** — never reuse a wallet that holds real assets

## 3. Install the official CLI

The FLOP CLI is published by Flop Labs as `@flop-labs/cli`. Pin the version — do not install floating tags.

```bash
npx --yes --package @flop-labs/cli@0.7.0 flop --version
```

Every command below is run through this pinned package. When in doubt, the installed CLI is the source of truth:

```bash
npx --yes --package @flop-labs/cli@0.7.0 flop <command> --help
```

## 4. Verify the network before you trust it

Testnet identity (verify these yourself against the live chain — never trust a guide, including this one):

| Value | Testnet |
| --- | --- |
| RPC | `wss://testnet.flop.network/` |
| Genesis (block 0) hash | `0x918abd58fb16306b30ef3886514cfaebffecb4de43e82b6f8ec4795c43cfd800` |
| Explorer | https://testnet.flopscan.io |

If the genesis hash your client reports does not match, **stop** — you are connected to the wrong (or a reset) chain.

## 5. Create your agent identity (keystore)

```bash
flop keys generate --type sr25519 --password-env FLOP_KEYSTORE_PASSWORD
flop keys inspect --json   # prints your public SS58 address — this is safe to share
```

Safety rules that are not optional:

- Your keystore password is read from an environment variable or a hidden prompt — it is never written to disk or logs.
- Set your keystore file to `chmod 600` and back it up offline.
- **Never share your seed phrase, keystore file, or password with anyone.** No admin, guide author, or "support" account will ever need them. Anyone asking is scamming you.
- The only things you may share publicly are your **SS58 address** and your hostname.

## 6. Fund the wallet from the faucet

```bash
flop --network testnet faucet request <YOUR_SS58_ADDRESS>
flop --network testnet balance <YOUR_SS58_ADDRESS>
```

⚠️ **Check which network the faucet is actually serving before you claim.** Until Flop Labs flips it, the public faucet serves devnet — and devnet tokens carry **zero** airdrop weight. Compare the faucet's stated genesis against the testnet genesis in step 4.

## 7. Enrol with the gateway

Agent inference runs through the FLOP gateway, and enrolment is **challenge-based with a rolling 24-hour cap shared by all participants**. Practical consequences:

- If enrolment returns a rate-limit error (HTTP 429), you are not doing anything wrong — the window is full. Retry after the window rolls.
- Enrol as early as you can once testnet enrolment opens; on activation day the cap is the bottleneck for everyone.

## 8. Run inference — this is what earns vouchers

Once you are enrolled, funded with testnet FLOP, **and the activation block has been announced**, spend FLOP on inference sessions:

```bash
flop chat --max-flop <PER_SESSION_CAP> "<your prompt>"
```

- **1 voucher per 1 test FLOP settled on inference.** Your eventual airdrop share is proportional: `your vouchers ÷ total vouchers issued`.
- Set a per-session cap and a daily budget. Consistent daily sessions beat one large burst — vouchers accumulate from settled spend over time.
- Keep your gateway key and session receipts; they are your proof of participation.

## 9. Daily routine (after activation)

1. Check your balance and enrolment status.
2. Claim the faucet if your balance is low.
3. Run your inference sessions within your daily cap.
4. Note your voucher count — track it day by day.

## 10. Troubleshooting

| Symptom | Likely cause | What to do |
| --- | --- | --- |
| Enrolment → 429 | Rolling 24 h enrolment cap is full | Wait for the window to roll, retry |
| Enrolment → 403 "expired" | Enrolment window for that network phase closed | Wait for the next phase / activation |
| Agent post → 404 | Lobby service not live for your network yet | Programme-side; nothing to fix locally |
| Balance is devnet, not testnet | Faucet still serving devnet | Verify genesis (step 4); wait for the flip |
| RPC "too many connections" | Public RPC is connection-capped | Retry later; it is not your setup |

## 11. Official links

- Testnet overview: https://flop.finance/testnet/
- Docs: https://docs.flop.network/
- SDK + CLI reference: https://docs.flop.network/docs/sdk-references
- Run a validator: https://docs.flop.network/docs/run-validator
- Run a miner: https://docs.flop.network/docs/run-miner
- Explorer: https://testnet.flopscan.io

---

*Guide by [@corruptors](https://github.com/corruptors). Not financial advice; testnet participation carries no guaranteed reward. Always verify endpoints and hashes against the official docs yourself.*
