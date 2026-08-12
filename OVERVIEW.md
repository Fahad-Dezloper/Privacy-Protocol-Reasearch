# Swish — Overview

## What It Is

Privacy-focused payments app on Solana. Lets users send/receive USDC without exposing wallet addresses or amounts on-chain.

**Problem it solves:** Solana is fully transparent — every tx shows both wallet addresses and amount to anyone. Swish breaks that link.

## Core Value Props

- Send USDC to anyone privately (wallet addr or X handle)
- Receive via payment request link — payer can't see your wallet
- Send via claimable link — no recipient wallet needed upfront
- Zero Swish fees — only protocol fees apply
- Gas-sponsored withdrawals

## Who It's For

Solana users who don't want financial activity publicly linked to their wallet identity.

## Asset & Chain

| | |
|---|---|
| Blockchain | Solana |
| Currency | USDC only (stable, no volatility) |
| Network | Mainnet |

## Auth Options

1. **Solana wallet** — Phantom, Solflare, Backpack, Seeker
2. **X (Twitter)** — auto-generates embedded Solana wallet, no wallet app needed

Session signature on login → used locally to derive encryption keys. Never sent to blockchain. Zero on-chain footprint from auth.

## Platforms

- Web: swish.cash
- Mobile: Solana dApp Store (via Seeker)
- Same account across both if same X/wallet used

## What Is NOT Private

Deposits and withdrawals are standard Solana USDC transfers — both addresses visible on-chain. Only Send/Request/Claim flows have privacy guarantees.
