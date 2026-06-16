# MagicBlock — What Happens When an Operator Fails / Dies

> How MagicBlock's Ephemeral Rollups handle a validator/operator that crashes, stalls, or goes
> offline permanently. Sourced from MagicBlock's **Dynamic Fraud Proof** whitepaper (Picco &
> Fortugno), the Delegation/Commitment/Undelegation docs, and the `delegation-program` source.

## Core principle

> **Solana (the base layer) is always the ultimate source of truth. The Ephemeral Rollup (ER) is
> just a fast helper.** The worst a crash can do is cost the *very recent, not-yet-committed* work
> — never an already-saved balance.

## Two kinds of "crash"

### 1. Temporary hiccup / restart
- **Commit** = a periodic checkpoint where the ER writes its latest state back to Solana
  (frequency is configurable — seconds).
- On restart, everything **up to the last commit is safe** on Solana; the ER reloads that state
  from the chain and continues.
- Only transactions made in the ER **since the last commit and not yet committed** are rolled back
  (as if they never happened). Frequent commits keep this loss window small (seconds).
- **Analogy:** the back room catches fire — the last score copied into the town ledger still
  stands; only moves made after that last copy are gone. No official balance is destroyed.

### 2. Operator goes down permanently (the real worry)
A plain ER is run by **one operator** (a single sequencer). While delegated, an account is
**locked** — only that operator may commit it back or release it. So if the operator dies for
good, already-committed funds are still safe on Solana, but the account could be **stuck** because
the normal `undelegate` needs the operator to sign.

This is the classic single-sequencer trade-off: a TEE/ER gives you **speed, integrity, and
confidentiality — but not guaranteed *availability*.** The operator can't *steal* or *secretly
alter* funds (the chain enforces that), but it *can* stall you by going offline.

## How the system is *designed* to handle a dead operator (5 safety nets)

1. **Delegation has a maximum lifetime.** Delegation metadata includes *"the maximum lifetime of
   the delegation."* Accounts *"are locked on the base layer and can only be updated or
   undelegated when specific conditions are met."* The lock is time-bounded by design.

2. **Data Availability (DA) Layer.** A component that *"stores the transactions executed in the
   ephemeral session."* The activity record is saved **outside** the operator's machine, so state
   can be **reconstructed** without the original operator.

3. **Provisioner can reassign the operator.** The Provisioner *"listens for delegation events,
   selects operators, and manages runtime provisioning"* — *"a market of supply and demand."* A
   dropped session can be handed to a **different operator**, which rebuilds state from the DA
   layer + last commit and resumes (or finalizes so the account can be undelegated).

4. **A cheating/stalling operator cannot finalize bad state (inverted challenge).** Unlike normal
   optimistic rollups (assert fake state, then censor objections), MagicBlock requires a
   *"configurable number of randomly selected"* watchers (the **Security Committee** / challengers)
   to **actively sign off** before finalization. *"If the threshold for approval is not met when
   the challenge period expires, the state cannot be finalized, and the challenge period is
   extended."* Silence ≠ approval, and **anyone** may raise a challenge. The window starts tiny
   (example `t0 = 500ms`) and stretches exponentially if approvals are missing (a degraded
   scenario works out to ~7 minutes vs. the 7 *days* other rollups use).

5. **Solana is the final backstop.** The **Delegation Program** lives on Solana and is what
   *"locks/unlocks accounts… and is responsible for finalizing the state after the security
   conditions are satisfied."* The base chain — not the operator — holds the keys to the lock.

## ⚠️ Honest gap: design vs. what's live today

- The five mechanisms above are the whitepaper's **full vision** — a permissionless operator
  market, security committee, DA layer, and staking/slashing tied to the `$BLOCK` token.
- In the **actual deployed code**, live `undelegate` still requires the assigned **operator
  (validator) to sign**, plus there is an **admin-only** force-release path
  (`undelegate_confined_account`) controlled by MagicBlock.
- Today MagicBlock runs a **small set of its own regional operators** — not yet the decentralized
  market the paper describes.

**In practice right now:** "the operator died" mostly means "MagicBlock's own infrastructure had
an outage," and recovery currently leans on **MagicBlock + the base-layer last commit + their
admin path** — *not* yet the fully trustless force-recovery. The decentralized version is the
**roadmap**, not the present.

## Bottom line for Swish

The architecture is genuinely designed so a dead operator can't steal funds or lock them forever:
saved state is on Solana, data survives in the DA layer, another operator can take over, and bad
state can't auto-finalize. But the **trustless** version of that recovery is still maturing —
today there's real reliance on MagicBlock as operator/admin. This is a **liveness + centralization
risk** to weigh when depending on the MagicBlock route.

## Sources
- MagicBlock Dynamic Fraud Proof whitepaper — https://docs.magicblock.gg/public/Ephemeral_Rollups_Fraud_Proof.pdf
- Delegation, Commitment & Undelegation — https://docs.magicblock.gg/pages/ephemeral-rollups-ers/introduction/ephemeral-rollup
- `magicblock-labs/delegation-program` source (undelegate / undelegate_confined_account processors)
