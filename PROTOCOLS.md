## MagicBlock

**Type:** TEE (Trusted Execution Environment) via Intel TDX

**How it works:**
Transaction processed inside a hardware secure enclave. The enclave isolates computation — even the node operator can't see what's happening inside. Breaks on-chain link between sender/receiver.

**Cost:** Gas only (~free)

**Requirements:** None. Works for any sender/recipient.

**Best for:** Standard private sends where both parties have wallets. Default choice.

---

## Umbra

**Type:** ZK shielded pool with MPC

**How it works:**
Both parties register with Umbra protocol. Sends go into a shielded pool. Recipient claims with ZK proof. MPC (multi-party computation) involved in key management.

**Cost:** 0.7% fee charged at claim

**Requirements:** BOTH sender AND recipient must be Umbra-registered.

**Best for:** Frequent senders/receivers who both use Swish regularly.

**Not available for:** Send via Claim (recipient unknown upfront).

---

## Privacy Cash

**Type:** ZK UTXO mixer

**How it works:**
Sender deposits funds into a shared anonymity pool. Pool mixes funds with others. Recipient (or sender on their behalf) withdraws using a zero-knowledge proof — proves they have right to withdraw without revealing deposit link.

**Cost:** ~$0.71 + 0.35% of amount (base = 0.006 SOL in USDC)

**Requirements:** None. Any recipient — even without Swish.

**Best for:** Sending to non-Swish users, fallback when others unavailable.

---

## Protocol Selection Matrix

| Scenario                      | Available Protocols                              |
| ----------------------------- | ------------------------------------------------ |
| Send to registered Swish user | Auto / Umbra / MagicBlock / Privacy Cash         |
| Send to non-Swish wallet      | Auto / MagicBlock / Privacy Cash                 |
| Send via Claim                | Auto / MagicBlock / Privacy Cash                 |
| Request                       | Auto / MagicBlock / Privacy Cash (payer chooses) |

---

## Auto-Routing Logic

```
Is recipient Umbra-registered?
  Yes → Try Umbra
  No  → Skip

→ Try MagicBlock (always available, cheapest)

→ Fallback: Privacy Cash (universal, but has flat fee)
```

If selected route fails mid-tx → automatically tries next in chain.
