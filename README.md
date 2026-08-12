# Swish Research Docs

Research notes for building on top of / understanding Swish (swish.cash).

## Files

| File | Contents |
|---|---|
| [OVERVIEW.md](./OVERVIEW.md) | What Swish is, who it's for, core props |
| [ARCHITECTURE.md](./ARCHITECTURE.md) | Tech design, routing logic, web vs mobile |
| [PROTOCOLS.md](./PROTOCOLS.md) | Privacy Cash / MagicBlock / Umbra deep dive |
| [FEATURES.md](./FEATURES.md) | Send / Request / Claim / Deposit / Withdraw flows |
| [FEES.md](./FEES.md) | Fee structure per protocol |
| [FAQ.md](./FAQ.md) | All FAQ Q&As from official docs |
| [SOLANA_PRIVACY_PROTOCOLS.md](./SOLANA_PRIVACY_PROTOCOLS.md) | Full Solana privacy protocol landscape (beyond Swish's 3) |
| [VERIFICATION_LAYER.md](./VERIFICATION_LAYER.md) | Privacy verification layer — idea, architecture, per-protocol research plan |
| [protocols/PRIVACY_CASH.md](./protocols/PRIVACY_CASH.md) | Privacy Cash — full protocol deep dive (ZK-UTXO, flow, terminology) |

## TL;DR

Swish = privacy payments aggregator on Solana.
- Asset: USDC only
- Privacy via 3 protocols: MagicBlock (free TEE), Umbra (ZK+MPC, 0.7%), Privacy Cash (ZK mixer, ~$0.71+0.35%)
- Auto-routes to cheapest viable option
- Swish charges zero
- Auth: Solana wallet or X (Twitter)
- Source: https://docs.swish.cash
