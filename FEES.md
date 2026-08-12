# Swish — Fees

## Swish Platform Fee

**Zero.** Swish charges nothing.

---

## Protocol Fees

| Protocol | Fee | Notes |
|---|---|---|
| MagicBlock | Gas only (~free) | Cheapest option |
| Umbra | 0.7% at claim | Only if both parties registered |
| Privacy Cash | ~$0.71 + 0.35% | Base = 0.006 SOL converted to USDC |
| Withdrawal | Free | Gas sponsored by Swish |

---

## Who Pays

- **Send:** Sender pays protocol fee
- **Request:** Payer (not the requester) pays protocol fee
- **Send via Claim:** Sender pays at link creation

---

## Fee Transparency

Every tx modal shows exact fee breakdown and net amount recipient receives before confirmation. No surprises.

---

## Auto-Routing Cost Logic

Auto mode favors cheapest viable route:
- MagicBlock first (gas only)
- Umbra if both registered (0.7% — still cheap at small amounts)
- Privacy Cash as fallback (~$0.71 flat makes it expensive for tiny sends)
