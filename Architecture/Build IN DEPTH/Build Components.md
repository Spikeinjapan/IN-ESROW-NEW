

<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/7387ab00-fc10-4e5e-9a2d-7e52571cfd6b" />




# Dual-Witness Hybrid-Attestation Network
### Qubic Vault Escrow Protocol · v6.1

> **This is not a bridge.** It is a bounded-liability asynchronous redemption clearinghouse — a fundamentally safer cross-chain collateral model.

[![Version: v6.1](https://img.shields.io/badge/Version-v6.1-black)](.)
[![Network: Qubic](https://img.shields.io/badge/Network-Qubic-brightgreen)](https://qubic.org)
[![Chains: BNB · BASE](https://img.shields.io/badge/Chains-BNB%20·%20BASE-F0B90B)](https://www.bnbchain.org)
[![Status: Pre-Deployment](https://img.shields.io/badge/Status-Pre--Deployment-yellow)](.)
[![Audit: Post-Build](https://img.shields.io/badge/Audit-Post--Build-orange)](.)
[![Confidential: Architecture Reference](https://img.shields.io/badge/Confidential-Architecture%20Reference-red)](.)

---

## Table of Contents

- [The 1+1=Immunity Principle](#the-11immunity-principle)
- [Traditional Bridge Risk Eliminated](#traditional-bridge-risk-eliminated)
- [System Architecture](#system-architecture)
- [Logic Flows](#logic-flows)
- [Fundraising & Normal Operations](#fundraising--normal-operations)
- [Security Model](#security-model)
- [zkBridge vs Dual-Witness](#zkbridge-vs-dual-witness)
- [Governance & Dispute Resolution](#governance--dispute-resolution)
- [Build Readiness & Deployment Profile](#build-readiness--deployment-profile)

---

## The 1+1=Immunity Principle

Security does not come from verifying foreign-chain consensus internals. It comes from requiring two entirely independent infrastructure pathways to independently derive and sign an **identical 128-byte standardised payload**.

```
Trusted Relayer  ──┐
                   ├──► Hash_R == Hash_O ? → RELEASE : CIRCUIT BREAKER
Oracle Pool      ──┘
```

The vault releases collateral **if and only if both signatures match.** Compromising one pathway produces a mismatch — not a successful attack.

---

## Traditional Bridge Risk Eliminated

| Attack Vector | Status |
|---|---|
| Infinite mint / inflation exploits | ✅ Eliminated |
| Wrapped asset inflation | ✅ Eliminated |
| Arbitrary cross-chain execution | ✅ Removed |
| Generalised cross-chain messaging | ✅ Removed |
| Dynamic supply expansion | ✅ Removed |
| Real-time solvency synchronisation | ✅ Removed |
| Hot multisig admin keys | ✅ Removed |
| Upgradeable vault logic | ✅ Removed |
| First-come bank runs | ✅ Eliminated |
| Snapshot timing attacks | ✅ Removed |

---

## System Architecture

The protocol operates across three distinct layers. No layer has authority to act unilaterally — every consequential action requires cross-layer agreement.

```
┌──────────────────────────────────────────────────────────────────┐
│                     EVM LAYER (BNB / BASE)                       │
│                                                                  │
│   ┌──────────────────────┐    ┌──────────────────────┐          │
│   │   Fortknox Vault A   │    │   Fortknox Vault B   │          │
│   │  BNB/BASE · capped   │    │  BNB/BASE · capped   │          │
│   │ Check: R == O ✓      │    │ Check: R == O ✓      │          │
│   └──────────┬───────────┘    └───────────┬──────────┘          │
└──────────────┼────────────────────────────┼────────────────────-┘
               │        sig A    sig B       │
               │           ↑      ↑          │
┌──────────────┼───────────┼──────┼──────────┼────────────────────┐
│              │    BRIDGING & ATTESTATION LAYER                   │
│              │                                                   │
│   ┌──────────┴────────┐      ┌───────────────────────┐          │
│   │   Trusted Relayer │      │     Oracle Pool        │         │
│   │  Continuous sync  │      │  Commit-Reveal · 10/15 │         │
│   │  Fast path        │      │  threshold             │         │
│   └──────────┬────────┘      └───────────┬───────────┘          │
│              │       ■ COMPARE ■          │                      │
└──────────────┼────────────────────────────┼────────────────────-┘
               │  surrender proof · event index                    │
               ↓                                                   │
┌──────────────────────────────────────────────────────────────────┐
│                        QUBIC LAYER                               │
│                                                                  │
│         ┌──────────────────────────────────────┐                │
│         │     Qubic Factory Smart Contract      │               │
│         │  Static array · 3-Phase Commit-Reveal │               │
│         │  qClaim token · RecoveryVaultAddress   │              │
│         └──────────────────────────────────────┘                │
└──────────────────────────────────────────────────────────────────┘
```

### Layer Responsibilities

| Layer | Component | Role | Trust Level |
|---|---|---|---|
| EVM | Fortknox Vault A/B/C | Hold collateral · verify dual signatures · execute release | Passive — no independent logic |
| Bridge | Trusted Relayer | High-speed sync · event tracking · payload publication | Independent infrastructure |
| Bridge | Oracle Pool | Commit-Reveal committee · threshold signing · independent indexing | Randomised · 10/15 threshold |
| Qubic | Factory SC | State machine · qClaim token · RecoveryVaultAddr · TWAP oracle | **Primary trust root** |

---

## Logic Flows

### Full Redemption Flow

Steps 1–2 occur on Qubic. Steps 3–4 occur in the bridging layer. Steps 5–7 occur on the EVM vault.

```
[1] Surrender          [2] Commit-Reveal      [3] Ingest
User sends qClaims  →  3-Phase CRS generates  →  Relayer + Oracle
to RecoveryVaultAddr   Master Seed +              independently
+ H(secret)            committee                  parse payload

[4] Sign               [5] Deliver            [6] Verify            [7] Release
Both sign identical →  Permissionless      →  Vault checks:      →  Payout =
128-byte hash          relayers carry         Hash_R == Hash_O      qClaims ×
(Nullifier included)   both sigs to vault     + nullifier clean     Recovery Ratio
                                                                     to EVM wallet

                                              ↓ MISMATCH
                                              Circuit breaker fires
                                              SC Shareholder arbitration
```

### Depeg Escalation Ladder

All price monitoring uses **Time-Weighted Average Prices exclusively.** Spot prices are never used for any vault decision.

| Stage | Trigger | Action |
|---|---|---|
| **Stage 1 · Watch** | TWAP < 95% for 1h | Silent log only |
| **Stage 2 · Warning** | TWAP < 90% for 24h | Public broadcast |
| **Stage 3 · Elevated** | TWAP < 85% for 72h | Node check frequency doubles |
| **Stage 4 · Declaration** | TWAP < 80% for 7d | Dual Vote opens |
| **Stage 5 · Emergency** | TWAP < 40% for 24h | **Auto circuit-breaker FREEZE** |

Each stage is triggered independently by a sustained TWAP threshold — not by discretionary governance action. Only Stage 4 opens a governance vote. Stage 5 is fully autonomous.

### Commit-Reveal Oracle Committee Selection

To prevent pre-bribery and targeted committee corruption, the oracle committee for each redemption is selected via a 3-Phase Commit-Reveal sequence:

```
Master Seed = H(User_Secret || E₁ || E₂ || ... || Eₓ)
Committee   = PRNG(Master Seed, Node Pool)
```

- **Phase 1 — Commit:** User submits blinded secret hash `H(User_Secret)` with their surrender transaction. Oracle nodes submit entropy commitments `Eᵢ` within the epoch window.
- **Phase 2 — Reveal:** All committed entropy values are revealed and combined with the user secret to produce the Master Seed. Unpredictable until reveal completes.
- **Phase 3 — Select:** Master Seed deterministically selects the active committee. Members are unknown before Phase 2 completes — preventing advance targeting.
- **Threshold:** 10-of-15 (67% supermajority) approvals required. No single node can produce or block a signature alone.

### 3-Epoch Liveness Safeguard

If a verification cycle stalls — due to user inactivity, oracle node failure, or any other interruption — the processing slot automatically resets to `STAGE_EMPTY` after 3 consecutive epochs without completion. Suspended qClaims are returned to the user's public wallet address. No funds are permanently locked by a failed verification attempt.

---

## Fundraising & Normal Operations

### Dual-Chain Fundraise Structure

| Parameter | Value | Notes |
|---|---|---|
| Increments (N) | 1 to 10 | Each increment = $81,200 gross ($80,000 net) |
| Maximum raise | $812,000 gross | Hard cap across all increments |
| Fee at close | 1.5% total | 1.0% to SC Shareholders · 0.5% to audit/update repo |
| Net proceed split | 60% LP / 40% investors | 60% seeds QSWAP liquidity · 40% to verified investors |
| Qu side target | ~40% of EVM deposit value | ±15% tolerance window at deposit time |
| Deposit window | 48-hour block horizon | EVM block heights + Qubic epoch parameters — no system clocks |
| Entry trigger | 200,000,000 Qu | Donation wallet threshold — SC unlocks subscriptions automatically |
| Wallet pairs | Immutable after activation | One EVM + one Qubic address per investor |

### 200M Qu Entry Trigger

A public donation wallet is established on Qubic at raise registration. Share subscriptions on both chains are **locked** until this wallet surpasses **200,000,000 Qu**. The SC automatically and trustlessly unlocks subscriptions the moment the threshold is surpassed — no human action required.

> If the threshold is not met, all donations are automatically refunded to originating wallets.

### Recovery Ratio

When Recovery Mode activates, the system computes a single **permanent scalar** that governs all subsequent redemptions:

```
Recovery Ratio = Total Actual Assets Locked in EVM Fortknox Vaults
                 ─────────────────────────────────────────────────
                       Total Outstanding Genesis qClaims Supply
```

This ratio is **immutable once set.** Every redemption uses this ratio. No user receives preferential treatment — losses are distributed pro-rata uniformly across all qClaim holders regardless of when they surrender.

---

## Security Model

### The 128-Byte Payload

Both the Trusted Relayer and the Oracle Pool independently compute and sign an identical 128-byte standardised payload:

```
Validation Hash = H(Source Chain || Dest Chain || Amount || Recipient || Nullifier)

Nullifier       = H(Tx_Hash || Sender || Amount || Nonce)
```

The nullifier is embedded in the payload and tracked in a **persistent bitmap filter** on the vault. Any replay of a settled redemption produces a used nullifier and is rejected immediately.

### Attack Resistance Analysis

| Attack Vector | Traditional Bridge | Dual-Witness | Why |
|---|---|---|---|
| Compromise one witness | Often sufficient | **Insufficient** | Mismatch triggers circuit breaker · no release |
| Pre-bribe committee | Possible if committee known early | **Prevented** | Committee revealed only after commit phase closes |
| Replay settled claim | Varies | **Eliminated** | Bitmap nullifier filter marks spent permanently |
| Drain via mint exploit | Catastrophic risk | **Eliminated** | Fixed genesis supply · no mint function |
| Admin key compromise | Often catastrophic | **Eliminated** | Ownership renounced · no admin keys exist |
| First-come bank run | Common failure mode | **Eliminated** | Pro-rata recovery ratio — all holders equal |
| Oracle collusion (majority) | High risk | **Moderate** | Requires simultaneous relayer + oracle compromise |

### Remaining Trust Assumptions

The system is not fully trustless. The following assumptions remain — narrower and easier to reason about than those in most conventional bridges:

| Assumption | Severity | Mitigation |
|---|---|---|
| Honest majority in oracle committees | Moderate | Randomised committees · commit-reveal · reputation scoring |
| Non-simultaneous compromise of relayer and oracle | Moderate | Decoupled infrastructure · independent operators |
| Correct payload canonicalisation | Moderate | Deterministic 128-byte schema · both sides compute independently |
| Underlying chain consensus integrity | Required | Cannot be mitigated — fundamental to any cross-chain system |
| Cryptographic hash security | Required | Standard assumption — same as all cryptographic systems |

---

## zkBridge vs Dual-Witness

The Dual-Witness model is not the strongest possible trust model — a fully trustless zkBridge would theoretically eliminate all oracle trust assumptions. However, such a system faces severe practical constraints targeting Qubic's execution environment.

| Property | zkBridge | Dual-Witness (this) |
|---|---|---|
| Full trustlessness | ✅ Stronger | ⚠️ Weaker — oracle assumptions remain |
| Practical buildability | ❌ Weaker | ✅ Stronger — 3-month deployment profile |
| Runtime complexity | ❌ Much higher | ✅ Much lower — flat payload comparison |
| Auditability | ❌ Harder | ✅ Easier — deterministic 128-byte schema |
| Operational survivability | ⚠️ Mixed | ✅ Strong — bounded failure modes |
| Qubic compatibility | ❌ Poor | ✅ Strong — native C++ execution model |

> **Strategic design choice:** The system intentionally optimises for survivable bounded failure rather than perfect cross-chain state equivalence. A bounded recovery event is far more manageable than an uncontrolled bridge collapse with elastic liabilities.

---

## Governance & Dispute Resolution

### Dual Vote Structure

Both votes must pass independently. Either gate failing blocks the unwind. Standard QSWAP trading continues normally during voting periods.

| Vote Gate | Quorum | Threshold | Window | Result if Fails |
|---|---|---|---|---|
| SC Shareholders (first) | 33% of SC shares | Simple majority | 7 days | Unwind blocked · Option A applied |
| Investors (second, if SC passes) | 30% of investor shares | Simple majority | 7 days | Unwind blocked · Option A applied |

> No discretionary emergency freezes are permitted during voting periods.

### SC Shareholders as Arbitrators

SC Shareholders serve as the binding arbitration layer for all disputes — including mismatch events, oracle disagreements, Trustee failures, and cross-chain relay anomalies.

All arbitration evidence **must be on-chain**: TWAP snapshots, oracle attestations, payload comparison records, and transaction history. Arbitration on off-chain information alone is not permitted. Decisions are final and binding.

### Mismatch Circuit Breaker

If `Hash_Relayer != Hash_Oracle` for the same redemption request:

1. The affected vault segment immediately fires an emergency circuit breaker and pauses
2. SC Shareholders receive an automated alert
3. SC Shareholders have authority to investigate, clear, or escalate
4. The vault does not resume until the mismatch is resolved and the circuit breaker is explicitly cleared

---

## Build Readiness & Deployment Profile

All components are buildable now. Running streams A (Qubic SC), B (EVM), and C (off-chain daemons) in parallel compresses calendar time to approximately **18–22 weeks to testnet**.

> ⚠️ The 128-byte payload schema must be specified and signed off **before Stream C begins** — it is the dependency that gates both the Trusted Relayer and Oracle Pool builds.

| Component | Status | Est. Weeks | Est. Cost @ $1,400/wk | Notes |
|---|---|---|---|---|
| Fortknox vault (Solidity) | ✅ Buildable now | 6 | $8,400 | Under 260 lines · no admin key |
| qClaim token SC (Qubic C++) | ✅ Buildable now | 4 | $5,600 | Fixed genesis supply · RecoveryVaultAddr |
| Qubic factory state machine | ✅ Buildable now | 10 | $14,000 | 9-state machine · tick-based |
| Dual-chain fundraise | ✅ Buildable now | 9 | $12,600 | Block horizon tracking · wallet pairs |
| TWAP oracle + escalation ladder | ✅ Buildable now | 7 | $9,800 | Oracle Machine dependency — confirm QPI |
| Dual-vote governance | ✅ Buildable now | 6 | $8,400 | SC Shareholder + Investor gates |
| Trusted Relayer daemon (Rust/Go) | ✅ Buildable now | 6 | $8,400 | Off-chain · event listener loop |
| Oracle Pool + Commit-Reveal | ✅ Buildable now | 9 | $12,600 | Randomness beacon · reputation scoring |
| 128-byte payload schema | ✅ Buildable now | 2 | $2,800 | Deterministic · both sides compute independently |
| EVM nullifier bitmap | ✅ Buildable now | 2 | $2,800 | Persistent spent-set in vault |
| QSWAP LP interface gate | ⚠️ Verify first | 2 | $2,800 | Confirm QSWAP router exposes `depositLiquidity()` |
| Full audit (EVM + Qubic + relay) | 🔒 Post-build | 14 | $19,600 | Include payload canonicalisation review |
| **TOTAL** | | **77 weeks** | **$107,800** | Sequential · parallel streams compress calendar |

---

## Disclaimer

> This document is confidential and intended for architecture reference purposes only. Nothing herein constitutes investment, legal, or financial advice. Participation in the protocol may involve significant risks. Users are responsible for complying with local regulations. Consult qualified legal and financial professionals before engaging with the protocol.
>
> Qubic is a decentralised, open-source network for experimental technology. Qubic does not offer securities.

---

<div align="center">

**Dual-Witness Hybrid-Attestation Network · Qubic Vault Escrow Protocol v6.1 · May 2026**

</div>
