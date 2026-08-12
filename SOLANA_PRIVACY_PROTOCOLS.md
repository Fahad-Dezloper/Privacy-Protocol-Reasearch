# Solana Privacy Protocols — Full Landscape

> Excludes Privacy Cash, MagicBlock, Umbra (already covered in PROTOCOLS.md).
> Status as of May 2026.

---

## Primitive Categories


| Type                  | What it does                                                       |
| --------------------- | ------------------------------------------------------------------ |
| ZK (ZK-SNARKs/STARKs) | Prove tx validity without revealing data                           |
| MPC                   | Multiple parties compute together — no single party sees full data |
| FHE                   | Compute directly on encrypted data — never decrypt                 |
| TEE                   | Hardware secure enclave — black box even to operators              |
| Stealth Addresses     | One-time addresses per tx — breaks address reuse linkability       |


---

## 1. Arcium (ex-Elusiv)

**Type:** MPC (Multi-Party Computation)
**Status:** Mainnet Alpha live — Feb 2026. Full mainnet + TGE Q1 2026.
**Stats:** 500+ nodes, 10,000+ daily confidential computations, 25+ projects building on mainnet.

**How it works:**
Decentralized network of nodes that compute over fully encrypted data. No single node sees the full input. Uses Multi-Party eXecution Environments (MXEs).

Two MPC backends:

- **Cerberus** — strongest security, requires ≥1 honest node
- **Manticore** — faster, weaker guarantees (optimized for AI/ML workloads)

Dev stack: arxOS (distributed OS), Arcis (Rust framework for MPC circuits), Rescue Cipher (symmetric encryption).

**Why relevant:**
Umbra (already in Swish) is built ON TOP of Arcium. Arcium is the infrastructure layer. Any protocol needing private multi-party computation builds here.

**Ecosystem apps on Arcium:** Umbra (shielded payments), Darklake (private AMM), Melee + Pythia (private prediction markets), Loyal (on-chain AI).

---

## 2. Solana Confidential Balances (Native Token Extension)

**Type:** ZK (ElGamal encryption + ZK proofs)
**Status:** Live on mainnet — April 2025. JS libraries for browser/mobile wallets coming later 2025.

**How it works:**
Built directly into Solana's token program as an extension. Three sub-extensions:

- **Confidential Transfers** — hides transfer amounts
- **Confidential Transfer Fees** — hides fee deductions
- **Confidential Mint/Burn** — hides supply changes

Flow: `Deposit` (public → confidential state) → `Apply` (finalize into available balance) → `Transfer` (move with ZK equality + validity + range proofs).

**Compliance:** Auditor Keys — institutionscan allow selective disclosure without breaking privacy for others.

**Key difference from mixers:** Addresses still visible — only AMOUNTS hidden. "Confidentiality not anonymity" is Solana Foundation's deliberate regulatory framing.

**Best for:** Institutional payroll, B2B payments, compliant DeFi.

---

## 3. Light Protocol

**Type:** ZK compression (SNARKs)
**Status:** Production. Pivoted from privacy → scaling layer.

**How it works:**
Compresses Solana account state using ZK proofs. Multiple account states compressed into one account. 128-byte ZK proof size constant regardless of accounts updated.

**Privacy angle:** Enables private state management at low cost — private NFT marketplaces, games with hidden state, private token transfers. Not a standalone privacy mixer but core infrastructure for building private apps cheaply.

**Cost reduction:** Up to 99% less on-chain storage vs uncompressed.

---

## 4. SilentSwap V2

**Type:** Unlinkable routing (not a pooled mixer)
**Status:** V2 live. Multi-chain (8 blockchains including Solana).

**How it works:**
Routes assets through multiple privacy layers creating unlinkable outputs. Never pools funds. Selective disclosure — users reveal data only to approved parties. Daily data purge — no persistent transaction records.

**Key features:**

- Send to up to 16 wallets in one tx
- 30 seconds to 2 minutes per tx
- ~1% fee
- OFAC-compliant framework
- Non-custodial

**Why it's different from mixers:** No shared pool = no "taint" risk from others in pool. Each swap is individually routed.

---

## 5. Encifher

**Type:** FHE (Fully Homomorphic Encryption)
**Status:** Active. (ZEC on Solana use case confirmed via CoinDesk Oct 2025)

**How it works:**
Re-wraps tokens into encrypted counterparts (e.g., eZEC). Encrypted assets can be swapped without ever decrypting on-chain. Ephemeral accounts — each account exists for exactly ONE transaction lifecycle, then discarded. Makes chain analysis effectively impossible.

**Key innovation:** Prevents transaction linkability + address reuse simultaneously.

---

## 6. GhostwareOS / GhostPay

**Type:** ZK-SNARKs + stealth addresses + relay routing
**Status:** Launched Solana mainnet Nov 2025.

**How it works:**
Hides sender, receiver, and amount. When receiver requests payment → GhostPay generates unique one-time payment link + QR code. Sender connects wallet → confirms. Relay routing obscures IP/metadata layer too.

**Token:** GHOST. 100% of protocol fees returned to GHOST holders.

**Positioning:** Competing with Zcash/Monero but as a Solana-native layer, not a separate chain.

---

## 7. Zama / Inco (FHE Infrastructure)

**Type:** FHE (Fully Homomorphic Encryption)
**Status:** Infrastructure/tooling layer.

**How it works:**
Arbitrary computation on encrypted data — never needs to decrypt. Most powerful privacy primitive but computationally expensive. Enables fully private smart contracts — inputs, outputs, and state all encrypted.

**Zama** = FHE tooling and libraries.
**Inco** = FHE network for private smart contract execution on Solana.

**Trade-off:** Slowest of all approaches. Used for complex logic where ZK/MPC isn't sufficient.

---

## 8. Darklake (Private AMM / zk-AMM)

**Type:** ZK (cryptographic commitments) + MPC (via Arcium)
**Status:** Live (blind slippage pool online).

**How it works:**
"Blind slippage pool" — cryptographic commitment layer on top of AMM. Slippage data invisible to MEV bots/searchers during tx, but verifiable on-chain after settlement.

**Why it matters:** Eliminates frontrunning / sandwich attacks at the protocol level. Not just payment privacy — DeFi execution privacy.

---

## 9. Lit Protocol

**Type:** Threshold cryptography / access control encryption
**Status:** Active.

**How it works:**
Encrypts keys and permissions using threshold signatures distributed across a network. Decryption only possible when conditions met. Extends privacy into data sharing, access control, and permissioned visibility.

**Use case:** "Who can see this data?" — not just "is this tx private?" Different privacy surface than payments.

---

## 10. Elusiv (Sunset)

**Type:** ZK-SNARKs
**Status:** DEAD. Sunset Feb 2024, withdrawal-only through Jan 2025.

**Note:** Team became Arcium. Same founders. Elusiv was Privacy 1.0 (isolated shielded pool). Arcium is Privacy 2.0 (shared private compute).

---

## Comparison Matrix


| Protocol              | Tech                | Hides                      | Status         | Fee    |
| --------------------- | ------------------- | -------------------------- | -------------- | ------ |
| Arcium                | MPC                 | Computation + state        | Mainnet Alpha  | -      |
| Confidential Balances | ZK (ElGamal)        | Amounts only               | Live           | Native |
| Light Protocol        | ZK SNARKs           | State (compression)        | Live           | Tiny   |
| SilentSwap V2         | Unlinkable routing  | Sender/receiver link       | Live           | ~1%    |
| Encifher              | FHE                 | Amounts + addresses        | Active         | -      |
| GhostPay              | ZK-SNARKs + stealth | Sender + receiver + amount | Live Nov 2025  | -      |
| Inco/Zama             | FHE                 | Smart contract logic       | Infrastructure | -      |
| Darklake              | ZK commitments      | MEV/slippage               | Live           | -      |
| Lit Protocol          | Threshold crypto    | Access/keys                | Active         | -      |
| Elusiv                | ZK-SNARKs           | Sender/receiver            | DEAD           | -      |


---

## Key Insight for Swish Direction

Swish aggregates payment privacy protocols. The landscape has expanded significantly:

- **Infrastructure layer:** Arcium (MPC supercomputer)
- **Native protocol layer:** Confidential Balances (amounts only, built-in, compliant)
- **Payment layer:** SilentSwap, GhostPay, Encifher
- **DeFi layer:** Darklake (private AMM), Encifher (private swaps)
- **Compute layer:** Zama/Inco (FHE for private contracts)

Biggest gap in current Swish: no FHE support, no DeFi/swap privacy (only USDC transfers), no MEV protection.