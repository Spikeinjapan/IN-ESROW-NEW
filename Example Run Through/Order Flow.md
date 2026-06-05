
<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/ed477f9c-5cba-4c56-848b-6723a573eab7" />



# qPEPE · Full Order Flow

> End-to-end lifecycle of a wrapped asset on the Dual-Witness Hybrid-Attestation Network — from token proposal through investor distribution and, if required, full protocol unwind.

---

## Table of Contents

- [Part 1 — Acquisition](#part-1--acquisition)
  - [Stage 1 · Registration & Setup](#stage-1--registration--setup)
  - [Stage 2 · The 200M Qu Entry Trigger](#stage-2--the-200m-qu-entry-trigger)
  - [Stage 3 · Smart Contract Auto-Unlock](#stage-3--smart-contract-auto-unlock)
  - [Stage 4 · The 48-Hour Deposit Window](#stage-4--the-48-hour-deposit-window)
  - [Stage 5 · Fundraise Close & Fees](#stage-5--fundraise-close--fees)
  - [Stage 6 · Distribution Split](#stage-6--distribution-split)
- [Part 2 — Depeg & Unwind](#part-2--depeg--unwind)
  - [Phase 1 · TWAP Escalation Ladder](#phase-1--twap-escalation-ladder)
  - [Phase 2 · Governance Dual Vote](#phase-2--governance-dual-vote)
  - [Phase 3 · Unwind Execution](#phase-3--unwind-execution)

---

## Part 1 — Acquisition

```
[1] Register   →   [2] 200M Qu Trigger   →   [3] Auto-Unlock   →   [4] 48h Window   →   [5] Close & Fees   →   [6] Distribution
```

---

### Stage 1 · Registration & Setup

| Action | Detail |
|---|---|
| Token proposal | Developer registers `qPEPE` on the Qubic Factory Smart Contract |
| Wallet setup | Public donation wallet is established on Qubic |
| Initial state | All subscriptions are **locked** — no deposits accepted yet |

---

### Stage 2 · The 200M Qu Entry Trigger

| Parameter | Value |
|---|---|
| Target | 200,000,000 Qu donated to the public wallet |
| Failure condition | If the target is not met, **all donations are automatically refunded** to originating wallets — no human action required |

> The 200M Qu raised at this stage is ringfenced exclusively for the QSWAP LP opening fee.

---

### Stage 3 · Smart Contract Auto-Unlock

```
Donation wallet balance > 200,000,000 Qu
            │
            ▼
Factory SC detects threshold surpassed
            │
            ▼
Subscriptions UNLOCKED — trustlessly, instantly, no admin key required
```

> No human action is required at this stage. The contract monitors and fires autonomously.

---

### Stage 4 · The 48-Hour Deposit Window

| Parameter | Value |
|---|---|
| Window duration | 48-hour block horizon |
| Clock mechanism | EVM block heights + Qubic epoch parameters — **no system clocks** |
| Deposit structure | Pre-allocated wallet pairs (one EVM + one Qubic address per investor) |
| Qu side target | ~40% of EVM deposit value ± 15% tolerance |
| Wallet pairs | Immutable after activation — cannot be changed mid-raise |

> Deposits outside the 48-hour block horizon are rejected by the contract.

---

### Stage 5 · Fundraise Close & Fees

```
48-hour window closes
        │
        ▼
Gross proceeds collected (per $81,200)
        │
        ├──► $800  →  SC Shareholders
        └──► $400  →  Updates & Audits Wallet
                              │
                              ▼
                     Net proceeds available for distribution
```

| Fee Recipient | Rate |
|---|---|
| SC Shareholders | `$800` |
| Updates & Audits Wallet | `$400` |
| **Total fee** | **`$1,200`** |

---

### Stage 6 · Distribution Split

```
Net proceeds
     │
     ├──► 40%  →  Verified Investors (direct allocation)
     │
     └──► 60%  →  QSWAP Router  →  qPEPE/Qu liquidity pool seeded
                                            │
                                            ▼
                                   qPEPE is LIVE on QSWAP ✓
```

| Allocation | Share | Destination |
|---|---|---|
| Investor allocation | `40%` | Distributed to verified investor wallet pairs |
| Liquidity allocation | `60%` | QSWAP router — seeds the `qPEPE/Qu` pool |

> **Pre-deployment gate check required:** The 60% LP allocation requires the Factory SC to interact with the QSWAP SC programmatically via `depositLiquidity()`. If the router is unavailable at close, a fallback manual LP seeding path with SC Shareholder sign-off must be available.

---

## Part 2 — Depeg & Unwind

> Tokens remain **fully tradeable on QSWAP** throughout Phase 1 and Phase 2. No discretionary emergency freezes are permitted during a governance vote.

---

### Phase 1 · TWAP Escalation Ladder

All price monitoring uses **Time-Weighted Average Prices exclusively.** Spot prices are never used for any vault or governance decision.

| Stage | TWAP Threshold | Duration | Action |
|---|---|---|---|
| **Stage 1 · Watch** | < 95% of peg | 1 hour | Silent internal log only |
| **Stage 2 · Warning** | < 90% of peg | 24 consecutive hours | Automated public broadcast alert |
| **Stage 3 · Elevated** | < 85% of peg | 72 consecutive hours | Oracle node check frequency doubles automatically |
| **Stage 4 · Declaration** | < 80% of peg | 7 consecutive days | 7-day Governance Dual Vote opens automatically |
| **Stage 5 · Emergency** | < 40% of peg | 24 hours | 7-DAY governance in play |

Each stage is triggered independently by a sustained TWAP threshold — not by discretionary governance action. Only Stages 4 and 5 interrupt normal protocol operation.

---

### Phase 2 · Governance Dual Vote

Both gates must pass **independently and sequentially.** Either gate failing permanently blocks the unwind for that cycle and applies Option A.

```
Stage 4 / 5 triggered
        │
        ▼
┌─────────────────────────────────┐
│   Gate 1 · SC Shareholders      │
│   Quorum:    33% of SC shares   │
│   Threshold: Simple majority    │
│   Window:    7 days             │
└────────────┬────────────────────┘
             │
        ┌────┴────┐
      PASSES    FAILS
        │          └──► Unwind blocked · Option A applied
        ▼
┌─────────────────────────────────┐
│   Gate 2 · Investors            │
│   Quorum:    30% of inv. shares │
│   Threshold: Simple majority    │
│   Window:    7 days             │
└────────────┬────────────────────┘
             │
        ┌────┴────┐
      PASSES    FAILS
        │          └──► Unwind blocked · Option A applied
        ▼
  Unwind APPROVED
  → Proceed to Phase 3
```

> Standard QSWAP trading continues normally throughout the entire voting window. No discretionary freezes are permitted.

---

### Phase 3 · Unwind Execution

Once both gates pass, the protocol enters **Recovery Mode.** All subsequent redemptions use a single immutable scalar — no user receives preferential treatment.

---

#### Step 1 · Permanent Recovery Ratio Lock-In

```
Recovery Ratio = Total Actual Assets Locked in EVM Fortknox Vaults
                 ─────────────────────────────────────────────────
                       Total Outstanding Genesis qClaims Supply
```

This ratio is computed **once** and is **immutable from that point forward.** Every redemption — regardless of when the user surrenders — receives the same pro-rata ratio. Losses are distributed uniformly across all `qClaims` holders.

---

#### Step 2 · User Surrender (on Qubic)

```
User holds qClaims
        │
        ▼
Send qClaims  →  RecoveryVaultAddress
        +
Submit blinded secret hash  H(User_Secret)
```

The user submits both the `qClaims` and a blinded hash of their secret in a single transaction to the `RecoveryVaultAddress` on Qubic. The secret is not revealed at this step.

---

#### Step 3 · 3-Phase Commit-Reveal Committee Selection

To prevent pre-bribery and targeted committee corruption, the oracle committee is selected via a multi-party randomness beacon:

```
Master Seed = H(User_Secret || E₁ || E₂ || ... || Eₓ)
Committee   = PRNG(Master Seed, Node Pool)
```

| Phase | Action | Anti-Corruption Property |
|---|---|---|
| **Phase 1 · Commit** | Oracle nodes submit entropy commitments `Eᵢ` within the epoch window | Nodes commit before the user secret is revealed |
| **Phase 2 · Reveal** | All entropy values revealed + combined with `User_Secret` → `Master Seed` | `Master Seed` is unpredictable until reveal completes |
| **Phase 3 · Select** | `Master Seed` deterministically selects 15 active committee nodes | Committee members are **unknown** before Phase 2 closes — preventing advance targeting |

**Threshold:** 10-of-15 (67% supermajority) required to produce the oracle attestation signature. No single node can produce or block a signature alone.

---

#### Step 4 · Dual-Witness Ingest & Signing

```
qClaims surrender event
        │
        ├──────────────────────────┬──────────────────────────┐
        │                          │                          │
        ▼                          ▼                          │
 Trusted Relayer           Oracle Pool (10/15)                │
 Continuous sync           Commit-Reveal committee            │
 Independently parses      Independently parses               │
 payload                   payload                            │
        │                          │                          │
        ▼                          ▼                          │
 Signs 128-byte hash       Signs 128-byte hash                │
 H(Chain||Amount           H(Chain||Amount                    │
  ||Recipient||Nullifier)   ||Recipient||Nullifier)           │
        │                          │                          │
        └──────────────────────────┘                          │
                    │                                         │
                COMPARE                                       │
           Hash_R == Hash_O?                                  │
                    │                                         │
           ┌────────┴────────┐                               │
         MATCH           MISMATCH                             │
           │                 └──► Circuit breaker fires       │
           │                      Vault segment pauses        │
           │                      SC Shareholder arbitration ─┘
           ▼
   Proceed to Step 5
```

The **nullifier** embedded in each payload is defined as:

```
Nullifier = H(Tx_Hash || Sender || Amount || Nonce)
```

---

#### Step 5 · EVM Vault Gate Check

Permissionless relayers carry both signatures to the EVM Fortknox Vault. The vault performs two independent checks before any collateral moves:

| Check | Condition | Result if failed |
|---|---|---|
| Dual-witness match | `Hash(Relayer) == Hash(Oracle)` | Circuit breaker fires · vault segment pauses · SC Shareholder arbitration |
| Replay protection | Nullifier is clean against persistent bitmap filter | Transaction rejected immediately |

> The bitmap filter permanently marks every spent nullifier. Any attempt to replay a settled redemption is rejected at the vault level — no governance action required.

---

#### Step 6 · Collateral Release

```
Both checks pass
        │
        ▼
Vault executes release

Payout = qClaims × Recovery Ratio
        │
        ▼
Funds delivered to user's EVM wallet (BNB / BASE)
```

The payout is delivered directly to the EVM wallet address registered in the original immutable wallet pair. No further user action is required after Step 2.

---

### Fault Reference

| Fault | Where it fires | Response |
|---|---|---|
| `Hash_R ≠ Hash_O` at vault gate | Step 5 | Localized segment circuit breaker · vault section pauses · SC Shareholder arbitration |
| Oracle stall (node failure / user inactivity) | Step 3 | Processing slot resets to `STAGE_EMPTY` after 3 epochs · `qClaims` returned to user's public wallet |
| Dual Vote Gate 1 fails | Phase 2 | Unwind blocked · Option A applied · trading continues normally |
| Dual Vote Gate 2 fails | Phase 2 | Unwind blocked · Option A applied · trading continues normally |
| Stage 5 Emergency TWAP breach | Phase 1 | Autonomous circuit-breaker freeze · no governance vote required |

---

## Summary Flow (End-to-End)

```
ACQUISITION

Register qPEPE
    └─► Public donation wallet opens
            └─► Community donates Qu  →  200M Qu threshold met
                    └─► Factory SC auto-unlocks subscriptions
                            └─► 48-hour block horizon deposit window opens
                                    └─► Window closes  →  $1,200 fee deducted (per $81,200)
                                            └─► 40% → Investors
                                                60% → QSWAP LP
                                                        └─► qPEPE LIVE ✓


DEPEG & UNWIND (if required)

TWAP < 95% (1h)   →  Silent log
TWAP < 90% (24h)  →  Public broadcast
TWAP < 85% (72h)  →  Oracle frequency doubles
TWAP < 80% (7d)   →  Dual Vote opens
TWAP < 40% (24h)  →  Auto circuit-breaker. VOTE

    Dual Vote: SC Shareholders (Gate 1)  →  Investors (Gate 2)  →  Unwind APPROVED

Recovery Mode:
    Recovery Ratio locked (immutable)
        └─► User surrenders qClaims + H(secret) → RecoveryVaultAddr
                └─► 3-Phase Commit-Reveal selects oracle committee
                        └─► Trusted Relayer + Oracle Pool independently sign 128-byte payload
                                └─► Permissionless relayers deliver both sigs to EVM Fortknox Vault
                                        └─► Vault: Hash_R == Hash_O + nullifier clean?
                                                └─► RELEASE: qClaims × Recovery Ratio → EVM wallet ✓
```

---
