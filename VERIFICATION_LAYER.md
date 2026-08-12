# Privacy Verification Layer — Research & Build Plan

## The Idea

Private txs on Swish have zero proof. No receipt. No attestation. You sent $500 privately — no one can verify it happened, not even you cryptographically.

**Goal:** Build a verification layer that sits on top of all Swish privacy protocols. Sender + receiver can prove a tx happened — mathematically, cryptographically — without revealing anything to anyone else.

Works across: Send / Receive / Claim flows.

---

## Why It Doesn't Exist Yet (on Solana)

| Platform | What they have | Why insufficient |
|---|---|---|
| Zcash | Payment Disclosure + Viewing Keys | Zcash-only, single protocol, not Solana |
| RAILGUN | Private Proof of Innocence + Viewing Keys | Not on Solana |
| Solana Confidential Balances | Auditor Keys | One-way institutional only — not bilateral |
| Elusiv | Viewing keys (planned) | Dead |
| Swish / Privacy Cash / MagicBlock / Umbra | Nothing | Zero receipt system |

**Gap:** Solana-native, cross-protocol, bilateral private payment proof. Doesn't exist.

---

## Three Technical Approaches

### Option A — Commitment Hash (Simplest, ship fastest)
```
receipt = hash(sender_pubkey + receiver_pubkey + amount + nonce + timestamp)
```
- Store 32-byte commitment on-chain at tx time
- Both parties hold plaintext inputs
- Verify: recompute hash → matches on-chain = proof
- No ZK needed. Fast. Cheap.

### Option B — ECDH Shared Secret Receipt (Best balance)
```
shared_secret = ECDH(sender_privkey, receiver_pubkey)
receipt_hash  = hash(shared_secret + amount + slot + nonce)
```
- Only parties with their own private keys can recompute
- No ZK circuit needed
- On-chain: 32-byte hash only
- Unforgeable — attacker needs private key

### Option C — ZK Proof of Payment / zkPoP (Strongest, hardest)
```
ZK-SNARK: "I know (sender, receiver, amount, nonce)
           such that commitment = hash(inputs)
           and sender_sig is valid"
```
- Verifiable by anyone given the proof + public params
- Built via Light Protocol / Arcium
- Adds latency — proof generation not instant

**Build order:** A/B first → C later.

---

## Protocol-Specific Deep Dive (TODO)

Each protocol has different crypto backend. Need to find where to hook in the receipt generation.

### Privacy Cash — ZK UTXO Mixer
- [ ] How deposit commitment is structured
- [ ] What the withdraw ZK proof contains (inputs/outputs)
- [ ] Where to inject receipt hash — at deposit or withdraw?
- [ ] Is nullifier reusable as part of receipt preimage?
- [ ] On-chain program: can we write a side-car account at deposit time?

### MagicBlock — TEE (Intel TDX)
- [ ] What does the TEE output after tx execution?
- [ ] Intel DCAP remote attestation — can it produce a receipt?
- [ ] Is there any on-chain artifact from MagicBlock tx?
- [ ] Fallback: generate ECDH receipt client-side BEFORE routing to TEE
- [ ] Hardest case — TEE is a black box, no native ZK output

### Umbra — ZK + MPC (via Arcium)
- [ ] Arcium MXE execution — does it produce output artifacts?
- [ ] MPC output commitments — can receipt piggyback on existing proof?
- [ ] Umbra shielded transfer structure — what's on-chain?
- [ ] Can Arcium MXE be extended to co-produce a receipt alongside the transfer?

### Future Protocols (as integrated into Swish)
- [ ] SilentSwap — unlinkable routing, what artifacts exist?
- [ ] GhostPay — stealth addresses, can derive shared secret from ephemeral key?
- [ ] Encifher — FHE, ephemeral accounts — receipt before tx execution?
- [ ] Arcium-native protocols — easiest, MXE output hooks available

---

## Flows That Need Coverage

### Send
```
Sender → [Protocol] → Receiver
Receipt must prove: sender paid receiver X amount via protocol P at time T
```

### Receive (Request flow)
```
Payer → [Protocol] → Requester
Receipt must prove: payer fulfilled request R, requester got X amount
```

### Claim
```
Sender → [generates claim link] → Claimer
Receipt must prove: claim link L was created by sender, claimed by claimer, amount X
Special case: sender and claimer may be unknown to each other at link creation time
```

Claim is hardest — receiver identity unknown at send time. ECDH not possible upfront. Options:
- Passphrase-derived key: `receipt = hash(passphrase + claimer_pubkey + amount)` — computed at claim time
- Two-phase commitment: sender commits at creation, claimer adds their key at claim

---

## Real Use Cases

| Use case | Who needs it |
|---|---|
| Invoice proof | Freelancers, B2B — "I paid invoice #123" |
| Dispute resolution | "I sent $500, you say you didn't receive" |
| Tax attestation | Prove tx happened + amount without deanonymizing |
| Loan collateral proof | "I have X USDC in private txn history" |
| Credit/reputation | Private payment history as proof of reliability |
| Compliance | Selective disclosure to auditor without full deanon |

---

## The Hard Problem: Cross-Protocol Compatibility

Each protocol different crypto backend:

| Protocol | Backend | Receipt hook | Difficulty |
|---|---|---|---|
| Privacy Cash | ZK-UTXO mixer | At deposit/nullifier level | Medium |
| MagicBlock | Intel TDX TEE | Client-side pre-routing only | Hard |
| Umbra | ZK + MPC (Arcium) | MXE output hooks | Medium |
| Arcium-based | MPC | Native output hooks | Easy |
| GhostPay | ZK-SNARKs + stealth | Stealth key derivation | Medium |
| Encifher | FHE + ephemeral | Pre-tx client-side | Medium |

**MagicBlock is the problem child.** TEE = no native ZK output. Must generate receipt client-side before routing to enclave. Means receipt is a parallel artifact, not derived from the protocol itself.

---

## Proposed Architecture

```
┌─────────────────────────────────────────┐
│              Swish UI                   │
└────────────────┬────────────────────────┘
                 │
┌────────────────▼────────────────────────┐
│         Verification Layer              │
│  - Receipt generation (pre-tx)          │
│  - On-chain commitment store            │
│  - Proof verification logic             │
│  - Per-protocol receipt adapters        │
└──┬──────────┬──────────┬────────────────┘
   │          │          │
┌──▼──┐  ┌───▼───┐  ┌───▼──────┐
│ PC  │  │  MB   │  │  Umbra   │  ... more
└─────┘  └───────┘  └──────────┘
  ZK      TEE         ZK+MPC
```

Verification Layer responsibilities:
1. Pre-tx: generate receipt inputs (ECDH or commitment)
2. During-tx: route to protocol as normal
3. Post-tx: store commitment on-chain (Solana account, 32 bytes)
4. Verify-time: recompute + compare against on-chain commitment

---

## Next Research Steps

1. **Privacy Cash internals** — read source, find deposit/nullifier structure
2. **MagicBlock TEE** — what on-chain artifact does it leave? Is DCAP attestation accessible?
3. **Umbra / Arcium** — Arcium MXE output hooks docs
4. **Light Protocol** — can compressed accounts store receipts at near-zero cost?
5. **Zcash payment disclosure spec** — steal the design, port to Solana
6. **RAILGUN Proof of Innocence** — study their bilateral proof model

---

## Honest Assessment

**Build it.** Real gap. No Solana-native cross-protocol bilateral proof system exists.

**Start with:** Option B (ECDH receipt) for Privacy Cash + Umbra — these are ZK-based, cleanest integration. Ship fast.

**Then:** Figure out MagicBlock client-side receipt. Parallel artifact approach.

**Moat:** Not the cryptography (primitives are known). The standard. First Solana protocol to define the receipt format that Swish + future apps adopt.
