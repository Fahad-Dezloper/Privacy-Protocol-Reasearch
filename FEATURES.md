# Swish — Features

## 1. Send

Transfer USDC privately to known recipient.

**Recipient options:**
- Solana wallet address (manual or QR scan)
- X (Twitter) handle → Swish resolves to wallet address

**Flow:**
1. Enter amount
2. Enter recipient (address or X handle)
3. Pick protocol (Auto default, or manual override)
4. Review fees + net amount recipient gets
5. Approve tx
6. Appears in Activity tab

**Protocol availability:** Auto / Umbra / MagicBlock / Privacy Cash
Unavailable options dimmed based on recipient eligibility.

---

## 2. Request

Generate payment link to receive USDC without exposing your wallet to payer.

**Flow:**
1. Enter amount
2. Optionally add message (max 50 chars, e.g. "Dinner last night")
3. Review fees
4. Generate + share link
5. Payer opens link → sees amount + message + protocol options → signs into Swish → approves

**Privacy:** Sender's wallet address never visible to payer throughout process.

**Fee responsibility:** Payer covers protocol fees, not requester.

**Status tracking (Activity tab):**
- Open — awaiting payment
- Settled — completed
- Cancelled — manually cancelled

Requestor can cancel any Open request.

---

## 3. Send via Claim

Send USDC via shareable link — no recipient wallet address needed.

**Use case:** Recipient has no Solana wallet yet, or you don't know their address.

**Flow:**
1. Enter amount → tap Send → tap "Generate a claim link"
2. Get: claim link + passphrase
3. Share both to recipient (passphrase never stored — must be communicated separately)
4. Recipient opens link → authenticates in Swish → enters passphrase → taps Claim

**Protocol availability:** Auto / MagicBlock / Privacy Cash (Umbra excluded — requires pre-registration)

**Optional:** Add message up to 50 chars.

**Reclaim:** If unclaimed, sender can open the claim link and tap Reclaim to recover funds.

---

## 4. Deposit

Fund your Swish wallet.

- Accepted: USDC (for payments) + SOL (for gas fees — small amounts only)
- Access: Profile → tap avatar → Deposit
- Shows QR code + wallet address
- Network must be Solana — wrong network = lost funds

---

## 5. Withdraw

Move USDC out to any Solana address.

- Standard on-chain USDC transfer — NOT private
- Gas sponsored by Swish — completely free to user
- Access: Profile → Withdraw → enter address or scan QR → enter amount → approve
- Lands in seconds

**Warning:** Both your wallet and destination are on-chain visible. Use for moving to your own wallets only if privacy matters.

---

## 6. Wallet Export

Export embedded wallet private key (X sign-in users only).

- Web only (mobile redirects to web)
- Access: Profile → Wallet → Export
- Lets you use the auto-generated wallet in Phantom/Solflare/etc.
