# Privacy Cash

Exist to destroy onchain link between sender and reciever

sender sends money to the pool. reciever withdraws money from the pool.

## POOL

You can only deposit fixed sizes like 0.1 sol, 1 sol etc... not like this 137.42 because this unique number can be tracked

*   pool is a smart contract
*   accepts deposits
*   hold funds in escrow
*   release funds on vaild withdrawls
*   enforces rules automatically

pool stores commitments

## the NOTE

it has two parts
*   secret: proves the deposit is yours (hash(secret, nullifier) = commitment)
*   nullifier: prevents claiming it twice (fingerprint of hash(nullifier) = nullifier hash )

*   secret: 32 bytes
*   nullifier: 32 bytes
*   secret + nullifier: 64 bytes

## the commitment

commitment = hash(secret, nullifier) (0xslkdfjalsdkfjoaie) [impossible to reverse]
same input = same output but can't figure out the input
privacy cash uses the hash function called poseidon which is specifically design for zk circuits

## merkle tree

Pool starts empty. Each new deposit adds one leaf. Tree updates upward to new root.
commitment = new leaf

```text
After 1 deposit:              After 4 deposits:
      [R1]                          [R4]
      /                            /    \
  [c1] [ ]                    [H_12]  [H_34]
                               /  \    /  \
                              c1  c2  c3  c4
```

```text
You generate:   secret + nullifier = Note        (private, your device)
You compute:    hash(secret, nullifier) = commitment
Pool records:   commitment as a leaf in Merkle Tree
Pool updates:   Root on-chain = fingerprint of all deposits
```

At withdrawal:
*   You prove (privately):  "I know secret + nullifier
                             that hash to a commitment
                             that exists in the tree
                             that produced this root"
*   You reveal (publicly):  nothing except the proof is valid

### Timeline view:

```text
T=0    You open Privacy Cash
T=1    Browser generates secret + nullifier locally
T=2    Browser computes commitment
T=3    Browser encrypts Note with wallet-derived key
T=4    Encrypted Note stored on indexer
T=5    Deposit tx submitted → your wallet → pool
T=6    Contract adds commitment to Merkle Tree
T=7    New Merkle Root recorded on-chain
T=8    Done. Funds in pool. Note safe. Link broken.
```

## The Nullifier Hash (Anti Double-Spend)

## ZK SNARK

zero knowledge succinct non interactive argument of knowledge

When you withdraw, you need to convince the smart contract of THREE things:

1. I know a valid Note (secret + nullifier)
   that hashes to a commitment

2. That commitment exists in the Merkle Tree
   (valid Merkle path to the current root)

3. This nullifier hasn't been used before
   (fresh spend, not double-spend)

All three — without revealing your secret, your commitment, or your Merkle path.

### ZK proof actually is

A cryptographic blob. Small file. ~200 bytes.

It says nothing readable. Just math. But when the smart contract runs a verification function on it — it outputs one bit:

`valid ✓`   or   `invalid ✗`

That's it. The contract learns ONLY that the proof is valid. Nothing else.

Inside the proof — your secret, nullifier, commitment, Merkle path are all hidden. Mathematically sealed.

### how is it generated

Your browser runs a ZK circuit. Think of a circuit like a very specific math program that:

Takes private inputs:
*   secret
*   nullifier
*   Merkle path (your commitment's path to root)

Takes public inputs:
*   nullifier_hash  (hash of your nullifier)
*   Merkle root     (current on-chain root)
*   destination     (address receiving funds)

Produces:
*   a proof that all private inputs are consistent
    with the public inputs
*   without revealing any private input

The circuit checks:
*   ① Poseidon(secret, nullifier) = some commitment     ← valid Note
*   ② That commitment sits in the Merkle path           ← exists in pool
*   ③ Poseidon(nullifier) = nullifier_hash              ← correct nullifier
*   ④ destination = the address you specified           ← bound to this tx

### The SNARK part — why it's fast to verify.

Generating the proof — done on your device — takes a few seconds. Computationally heavy.

But verifying the proof — done by the smart contract — takes milliseconds. And costs almost nothing in gas.

```text
Proof generation:     your browser    ~2-5 seconds    heavy computation
Proof verification:   smart contract  ~milliseconds   cheap
```

This is the "Succinct" in SNARK. The proof is tiny and fast to verify regardless of how complex the underlying computation was.

## Withdrawl flow

### What's On-Chain After Withdrawal

**Public, visible to everyone:**
*   ✓  Someone withdrew 1 SOL from Privacy Cash
*   ✓  Destination: Bob's wallet
*   ✓  nullifier_hash: 0xABC123...
*   ✓  ZK proof blob
*   ✓  Merkle root used

**Not visible, doesn't exist on-chain:**
*   ✗  Which wallet originally deposited
*   ✗  Which commitment was spent
*   ✗  secret or nullifier
*   ✗  Merkle path

## The Relayer job

Every Solana transaction needs gas (SOL) to pay for computation. if i pay it then the privacy is broken as it will show the entry in ledger publically.

because it would look like this onchain

The withdrawal transaction would look like this on-chain:

```text
Fee payer:    9xK3...abc4  (your wallet)
Action:       withdraw from Privacy Cash
Destination:  Bob's wallet
```

relayer is a third party service that:
1. Recieve your signed proof off chain
2. wraps it in a txn
3. pays the gas from theri wallet
4. submits to blockchain

relayer gets paid as it takes small cut deducted from your withdrawal amount
```text
You deposited:   1 SOL
Relayer fee:     0.006 SOL  (Privacy Cash's fee structure)
Protocol fee:    0.35% of amount
Bob receives:    ~0.9905 SOL
```

Relayer is a dumb pipe. They submit exactly what you gave them. They can't modify it without breaking the proof.

---

## Privacy Cash — Learning Steps

*   **Step 1 — The Problem**
    Why Solana is public. What "breaking the link" means.

*   **Step 2 — The Pool**
    What a privacy pool is. Mental model.

*   **Step 3 — The Note (Secret + Nullifier)**
    The two random numbers generated at deposit.
    What each one does. Why both needed.

*   **Step 4 — The Commitment**
    What gets sent on-chain. Why it reveals nothing.
    How hash(secret + nullifier) = commitment works.

*   **Step 5 — The Merkle Tree**
    Why the pool needs a tree. What the root is.
    How it tracks all deposits without exposing them.

*   **Step 6 — Deposit Flow (end-to-end)**
    What happens step by step when you deposit.
    What goes on-chain vs stays local.

*   **Step 7 — The Nullifier Hash (anti double-spend)**
    Why you can't withdraw twice. How the contract knows
    without knowing which deposit you're spending.

*   **Step 8 — ZK-SNARK Proof**
    What the withdrawal proof actually proves.
    What it hides. What it reveals.

*   **Step 9 — Withdrawal Flow (end-to-end)**
    What happens step by step when you withdraw.
    Why destination address is never linked to depositor.

*   **Step 10 — The Relayer**
    Why you can't pay gas yourself at withdrawal.
    What relayer does. Trust model.

*   **Step 11 — Privacy Guarantees + What Breaks Anonymity**
    What's actually hidden. What's not.
    Patterns that leak info. Anonymity set.
