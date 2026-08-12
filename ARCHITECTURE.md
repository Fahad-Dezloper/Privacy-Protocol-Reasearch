# Swish — Architecture

## High-Level Design

Swish is a **privacy protocol aggregator** on Solana. Does not build its own crypto primitive — sits on top of 3 existing protocols and routes to cheapest/best viable one.

```
User → Swish UI → Auto-Router → [ Umbra | MagicBlock | Privacy Cash ] → Solana
```

## Privacy Protocols

### 1. MagicBlock (TEE via Intel TDX)
- Processes tx inside a Trusted Execution Environment (secure hardware enclave)
- Cheapest: gas only (~free)
- Fastest
- No recipient pre-registration needed
- Default choice for most sends

### 2. Umbra (ZK shielded pool + MPC)
- ZK-based shielded pool, requires both sender AND recipient to be Umbra-registered
- Fee: 0.7% at claim time
- Strongest privacy model
- Not available for Send via Claim (recipient unknown upfront)

### 3. Privacy Cash (ZK UTXO mixer)
- Deposit into shared pool → recipient withdraws with ZK proof
- Fee: ~$0.71 + 0.35% of amount
- Works with ANY recipient (no registration needed)
- Fallback option when others unavailable

## Auto-Routing Priority

```
Auto mode priority: Umbra → MagicBlock → Privacy Cash
```

- Umbra: only if both parties registered
- MagicBlock: standard fallback, cheapest
- Privacy Cash: last resort, works for anyone

Fails gracefully — if one route fails, tries next automatically.

## Send via Claim — Protocol Restriction

Umbra excluded from Claim links because Umbra requires knowing recipient ahead of time (pre-registration). Claim = recipient unknown = only MagicBlock or Privacy Cash.

## Key Cryptographic Detail

Login session signature → used locally to derive encryption keys for private txs. Never broadcast to chain. No wallet address exposure from auth flow.

## Mobile vs Web

| Feature | Web | Mobile |
|---|---|---|
| Multi-protocol routing | Yes (Auto across all 3) | No — Privacy Cash only (aggregator coming) |
| Wallet export | Yes | No (redirects to web) |
| Deep links (Request/Claim) | Opens in browser | Opens in app if installed |
| All core features | Yes | Yes |

## What Swish Handles

- Protocol selection + fallback logic
- Fee display pre-confirmation
- X handle → wallet address resolution
- Gas sponsorship for withdrawals
- Embedded wallet generation (X sign-in users)
- Claim link + passphrase generation (passphrase never stored)
