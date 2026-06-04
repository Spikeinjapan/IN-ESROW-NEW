# IN\_ESCROW — Cross-Chain Protocol

> **Secure, batch-processed asset wrapping for the Qubic ecosystem.**

[![License: MIT](https://img.shields.io/badge/License-MIT-brightgreen.svg)](LICENSE)
[![Network: Qubic](https://img.shields.io/badge/Network-Qubic-black?logo=data:image/svg+xml;base64,PHN2ZyB3aWR0aD0iMjQiIGhlaWdodD0iMjQiIHZpZXdCb3g9IjAgMCAyNCAyNCIgZmlsbD0ibm9uZSIgeG1sbnM9Imh0dHA6Ly93d3cudzMub3JnLzIwMDAvc3ZnIj48Y2lyY2xlIGN4PSIxMiIgY3k9IjEyIiByPSIxMCIgZmlsbD0id2hpdGUiLz48L3N2Zz4=)](https://qubic.org)
[![Smart Contract: SC-676](https://img.shields.io/badge/Governed%20By-676%20SC%20Shareholders-555)](https://qubic.org/governance)
[![Chains: BASE · BNB](https://img.shields.io/badge/Chains-BASE%20·%20BNB-0052FF)](https://base.org)
[![Status: Pre-Launch](https://img.shields.io/badge/Status-Pre--Launch-yellow)]()

---

## Overview

Emerging blockchain ecosystems — including Qubic — require deep liquidity from established EVM networks. Real-time cross-chain bridges remain one of the industry's most exploited attack surfaces, routinely resulting in multi-million dollar losses and acting as a barrier for both institutional and retail capital.

**IN\_ESCROW** replaces vulnerable real-time bridge mechanics with a **batch-settled, escrow-backed model** governed by shareholder decentralisation. Every wrapped asset issued on Qubic is fully backed 1:1 by native assets held in escrow. No real-time attack vectors. No developer dependency post-deployment.

---

## Table of Contents

- [Architecture](#architecture)
- [Shareholder Governance](#shareholder-governance)
- [Investor Allocations](#investor-allocations)
- [Fee Model & Economics](#fee-model--economics)
- [Tokenomics](#tokenomics)
- [Target Markets](#target-markets)
- [Roadmap](#roadmap)
- [Security](#security)
- [Contributing](#contributing)
- [Disclaimer](#disclaimer)

---

## Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                      EVM LAYER (BASE / BNB)                 │
│   User deposits native asset  →  Escrow contract locks it   │
└───────────────────────┬─────────────────────────────────────┘
                        │  Batch settlement (scheduled)
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                    ESCROW SETTLEMENT ENGINE                  │
│   Verifies reserves · Processes batch · Issues attestation  │
└───────────────────────┬─────────────────────────────────────┘
                        │  1:1 backed mint
                        ▼
┌─────────────────────────────────────────────────────────────┐
│                     QUBIC NETWORK                           │
│   Wrapped asset minted · QSWAP liquidity pool activated     │
└─────────────────────────────────────────────────────────────┘
```

### Key Design Principles

| Principle | Implementation |
|---|---|
| **Batch-Based Settlement** | Transactions processed in scheduled batches, eliminating real-time bridge attack vectors |
| **1:1 Native Asset Backing** | Every wrapped asset is fully collateralised by an equivalent native asset held in escrow |
| **Zero Developer Dependency** | All operational control transfers to SC Shareholders at launch |
| **Shareholder Governance** | 676 Smart Contract Shareholders govern all protocol decisions collectively |

---

## Shareholder Governance

Following deployment, IN\_ESCROW operates with **zero developer dependency**. All protocol control passes entirely to the **676 Smart Contract Shareholders**, who govern:

- Protocol upgrades and parameter changes
- Security enhancements
- Arbitration procedures
- Depeg and emergency recovery events
- Bug bounty review and adjudication

Decisions require shareholder consensus, consistent with Qubic's Computor-based quorum model (≥ 451 of 676).

---

## Investor Allocations

The protocol offers a **limited issuance of 200 Investor Liquidity Allocations**.

Capital committed to these allocations is **permanently locked** within protocol liquidity pools, creating deep and durable liquidity for wrapped assets. In exchange, investors receive a stacked fee return for the life of the protocol.

> ⚠️ Allocations are non-withdrawable. Investors receive yield, not redemption rights.

---

## Fee Model & Economics

IN\_ESCROW implements a **stacked fee model** across two revenue streams:

### Revenue Streams

| Source | Rate | Recipient |
|---|---|---|
| Standard LP Fee Share | `0.192%` per trade (64% of 0.30%) | Investor Allocations |
| QSWAP Premium Fee | `0.500%` per trade | Investor Allocations |
| **Total Investor Yield** | **`0.692%` per trade** | |

### Economics Example

Assuming **100,000,000,000 Qu** in trading volume:

```
Revenue = 100,000,000,000 × 0.692%
        = 692,000,000 Qu

Distributed dynamically across 200 Investor Allocation holders
within the relevant liquidity pool.
```

---

## Tokenomics

### Initial Fundraising Allocation

Mulitples of $81,200 ($80,000 Liq + $1200 fees)

| Allocation | Share |
|---|---|
| Core Liquidity Pools | `$80,000` |
| Smart Contract Shareholders | `$800` |
| Initial Update & Audit Pool | `$400` |

### QSWAP Premium Fee Distribution

Total additional trade fee: **0.70%**

| Recipient | Share |
|---|---|
| Investor Allocations | `0.50%` |
| Smart Contract Shareholders | `0.10%` |
| Updates & Audits Wallet | `0.10%` |

### Updates & Audits Wallet

The Updates & Audits Wallet receives a continuous revenue stream dedicated to:

- Smart contract upgrades
- Third-party security audits
- Anti-spam execution costs
- White-hat bug bounty programs
- Emergency protocol maintenance

All bounty submissions and security awards are reviewed and approved by Smart Contract Shareholders.

---

## Target Markets

Initial deployment targets high-volume, highly liquid assets from:

- **[BASE](https://base.org)** — Coinbase's L2 network
- **[BNB Chain](https://www.bnbchain.org)** — Binance's EVM-compatible chain

These chains were selected for their established retail demand and significant on-chain trading activity, maximising transaction throughput and fee generation from day one.

---

## Roadmap

```
Phase 1 · Deployment
  ├── Smart contract deployment on Qubic
  ├── Escrow infrastructure initialisation (BASE + BNB)
  └── 200 Investor Allocation issuance

Phase 2 · Handover
  ├── Developer keys revoked
  ├── Full control transferred to 676 SC Shareholders
  └── QSWAP premium fee activation

Phase 3 · Expansion
  ├── Additional EVM chain integrations (governance vote required)
  ├── Expanded wrapped asset catalogue
  └── Ongoing audits funded by Updates & Audits Wallet
```

---

## Security

IN\_ESCROW is designed from the ground up around a threat model that targets the most common real-time bridge exploits:

- **No real-time mint/burn hooks** — batch settlement removes the largest attack surface
- **Escrow-first model** — assets are locked before any wrapped token is issued
- **Immutable post-deployment** — no admin keys; all upgrades require shareholder consensus
- **Continuous audit funding** — the Updates & Audits Wallet ensures ongoing third-party review

