# Swish — FAQ

## General

**What is Swish?**
Privacy-focused payments app on Solana. Sends/receives USDC privately by routing through Privacy Cash, MagicBlock, and Umbra.

**What blockchain?**
Solana.

**What currency?**
USDC only. Stablecoin pegged to USD.

**Is it free?**
Swish charges zero. You pay only the underlying privacy protocol fee. Withdrawals are gas-sponsored — completely free.

**Which privacy protocol does Swish use?**
None exclusively — it aggregates all three (Privacy Cash, MagicBlock, Umbra). Picks cheapest viable route by default.

---

## Account & Wallet

**How do I create an account?**
Go to swish.cash → Connect Wallet. Sign in with Solana wallet (Phantom, Solflare, Seeker, etc.) or X (Twitter) account.

**What happens when I sign in with X?**
Swish auto-creates an embedded Solana wallet. No wallet app install needed.

**Can I use the same account on web and mobile?**
Yes — same X account or wallet = same Swish account and balance on both.

**Why sign a message after connecting?**
Session signature derives encryption keys for private txs. Not a blockchain tx. Costs nothing.

**Can I export my wallet?**
Yes (X sign-in users). Web only: Profile → Wallet → Export. Exports private key for use in Phantom/Solflare/etc.

---

## Sending & Receiving

**Is sending really private?**
Yes. Send/Request/Send via Claim all route through privacy protocols that break on-chain link between sender and receiver.

**Can I send to someone without Swish?**
Yes — any Solana wallet address works. For people without a wallet, use Send via Claim (they claim after making account).

**Send vs Send via Claim?**
- Send: direct to wallet address or X handle
- Send via Claim: generates link + passphrase, recipient claims funds later

**Can I cancel a request?**
Yes. Activity tab → cancel any Open request.

**Can I reclaim funds from a Claim link?**
Yes, if unclaimed. Open the claim link → tap Reclaim.

**What's Umbra and should I enable it?**
Umbra = one of the three protocols. Requires both sender and recipient registered. Optional — auto mode handles selection.

**Are withdrawals private?**
No. Withdrawals = standard USDC transfers on Solana. Both addresses visible on-chain. Gas is free though.
