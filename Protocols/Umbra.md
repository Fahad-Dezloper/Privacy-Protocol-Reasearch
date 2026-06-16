# Umbra

Umbra is NOT just a better Privacy Cash. It's a completely different model.
Umbra = private financial system. Encrypted balances, stealth addresses, two crypto layers working together. Much deeper.

Here are the steps for Umbra:

*   **Step 1 — How Umbra differs from Privacy Cash**
    Why the model is fundamentally different.
    What problems it solves that Privacy Cash doesn't.

*   **Step 2 — Registration + Master Viewing Key (MVK)**
    Why both sender AND receiver must register.
    What MVK is and what it does.

*   **Step 3 — Umbra Addresses (Stealth Addresses)**
    How unlinkable addresses are generated.
    Why observers can't link them to your main wallet.

*   **Step 4 — Two-Layer Privacy: MPC + ZK**
    Why Umbra needs BOTH layers.
    What each layer does that the other can't.

*   **Step 5 — The Mixer Pool (Anonymity Layer)**
    How deposits and claims work.
    Commitments, nullifiers, ZK proofs — same as Privacy Cash?
    What's different.

*   **Step 6 — Encrypted Balances (Confidentiality Layer)**
    What encrypted balances are.
    How your balance is hidden even from on-chain observers.

*   **Step 7 — The Hybrid Model (UTXO + Account)**
    How mixer pool and encrypted balances work together.
    Why this combination is powerful.

*   **Step 8 — Full Transfer Flow End to End**
    Sender → Receiver. Every step.

*   **Step 9 — Compliance + Viewing Keys**
    How Umbra handles audits and compliance.
    What viewing keys are and who can use them.

*   **Step 10 — Privacy Guarantees + What's Different from Privacy Cash**
    What Umbra hides that Privacy Cash doesn't.
    Weaknesses and tradeoffs.

## Umbra - private financial system

umbra takes into a private environment where evertything is private  as you interact with the platform.
*   balance hidden
*   transfer hidden
*   identity hidden

umbra uses two tech layers
*   **MPC (via Arcium)** — handles encrypted computation
*   **ZK proofs** — handles anonymous transfers

### what are stealth addresses?

### what are utxos?

### how umbra creates stealth addresses and what's its points?

### what the point of arcium mpc

### what is stealth vault

## registration + MVK ( Master Viewing Key )

Your MVK is the root key of your entire Umbra identity.

Everything in Umbra — every stealth address, every encrypted balance, every transfer you've ever made — can be derived or decrypted from this one key.

Think of it like a master password to a password manager. The manager holds hundreds of individual passwords. But one master password unlocks all of them.

### How MVK is generated.

Same pattern as Privacy Cash's Note — entirely local, never leaves your device:

```text
Your Solana wallet signs a message
         ↓
Signature used as master seed
         ↓
KMAC function (NIST-standardized PRF)
         ↓
MVK = your master key
```

Same wallet = same signature = same MVK. Always. Deterministic.

### How MVK is registered on-chain.

Your MVK needs to be linked to your Solana wallet on-chain so the system knows you're registered. But that link can't be public, otherwise anyone can see your wallet = your Umbra identity. So Umbra encrypts the link using Arcium's MPC

```text
Your Solana wallet address  +  Your MVK
              ↓
         Encrypted by Arcium MPC cluster
              ↓
         Stored on-chain (encrypted blob)
```

Nobody can read the blob except the MPC cluster — and even the MPC cluster can only decrypt it when specific conditions are met (like a legal compliance request).

### Why MPC for this — not just a regular hash?

MPC encryption CAN be decrypted — but only by the full cluster of independent nodes working together. No single node or Umbra itself can decrypt it alone.

```text
Regular encryption:   one key holder can decrypt → Umbra could spy on you
Hash:                 irreversible → compliance impossible
MPC encryption:       requires ALL nodes to cooperate → nobody alone can spy
                      but legally authorized decryption is possible
```

The two-step registration flow:

*   **Step 1 — Solana MVK Registration:**
    *   Sign message with main Solana wallet
    *   → generates MVK locally
    *   → encrypts [wallet ↔ MVK] via Arcium MPC
    *   → stores encrypted on-chain

*   **Step 2 — Each Umbra Address Registration:**
    *   Generate a new Umbra Address (stealth address) from MVK
    *   → register that address on-chain
    *   → now that address can receive funds

### Why receiver registration is required.
