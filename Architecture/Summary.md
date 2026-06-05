
# Dual-Witness Hybrid Attestation Network & Qubic Vault Escrow Protocol

## Project Overview

The Dual-Witness Hybrid Attestation Network (DWHAN) is a secure cross-chain asset onboarding protocol designed specifically for the Qubic ecosystem. Rather than using a traditional bridge architecture, the protocol introduces a collateralised batch escrow and attestation model that enables assets from BASE and BNB Chain to enter Qubic safely while eliminating many of the largest historical bridge attack vectors. The wrapperd token is tradeable within Qubic until a depeg unwind has signalled and actionated.

The protocol combines:

* Escrow-backed collateral vaults
* Fixed-supply wrapped asset issuance (qClaims)
* Dual independent attestation pathways
* Shareholder-governed recovery mechanisms
* QSWAP-native liquidity provisioning

Its goal is to become the primary secure gateway for external liquidity entering the Qubic ecosystem. 

---

# Problem

Qubic is an emerging blockchain ecosystem that requires access to deep external liquidity to support:

* Trading activity
* DeFi growth
* Stablecoin adoption
* New user onboarding
* Ecosystem expansion

Today, the most common solution is a traditional cross-chain bridge.

Unfortunately, bridges remain one of the most exploited categories in blockchain, with attacks frequently resulting in multi-million-dollar losses. These risks create significant barriers for both retail and institutional capital. 

Common bridge failures include:

* Infinite mint exploits
* Wrapped asset inflation
* Oracle manipulation
* Multisig compromise
* Real-time solvency failures
* First-come bank-run scenarios

The result is that liquidity providers and users often avoid newer ecosystems because of bridge security concerns. 

---

# Target Users

### Primary Users

* QSWAP traders
* Stablecoin holders
* Cross-chain liquidity providers
* Arbitrage traders
* Qubic DeFi participants

### Secondary Users

* Institutional liquidity providers
* Market makers
* Asset issuers
* Protocol treasuries

These users need secure access to external assets without relying on traditional bridge trust assumptions.

---

# Why Qubic

This protocol is specifically designed around Qubic's architecture and governance model.

Qubic acts as the system's primary trust root and hosts:

* Factory Smart Contract
* qClaim issuance
* Recovery vault management
* TWAP oracle logic
* Governance
* Shareholder arbitration mechanisms 

Unlike generic bridge solutions, DWHAN integrates directly with:

* Qubic Smart Contracts
* QSWAP liquidity pools
* Qubic shareholder governance
* Qubic-native token economics

The architecture was intentionally designed to maximize Qubic compatibility while remaining operationally practical and auditable. 

---

# Solution

The protocol replaces traditional bridge designs with a secure escrow-backed redemption system.

When users deposit collateral on supported EVM networks:

* Assets are locked in Fortknox Vaults
* qClaims are issued on Qubic
* Redemption requests require dual independent attestations
* Assets are released only when both witnesses produce identical signed payloads

The system uses:

### Witness 1

Trusted Relayer

### Witness 2

Decentralized Oracle Pool

Both independently compute and sign the same standardized 128-byte payload. The vault releases collateral only if both signatures match exactly. 

If either witness disagrees:

* No collateral is released
* Circuit breaker activates
* Shareholder arbitration begins 

This dramatically reduces single-point-of-failure risk.

---

# Why This Creates Qubic Adoption

The protocol directly generates:

* New external liquidity
* Additional trading volume
* New QSWAP pairs
* Stablecoin availability
* Increased smart contract usage
* Governance participation

Every deposit, redemption, liquidity provision, governance vote, and wrapped asset transaction generates measurable ecosystem activity.

This aligns directly with Qubic incubation goals around:

* Net new adoption
* Repeat usage
* Useful activity
* Sustainable value creation

---

# Funding Ask

We seek incubation support to complete:

### Stream A

Qubic Smart Contracts

* qClaim token
* Factory state machine
* Governance
* Recovery system

### Stream B

EVM Infrastructure

* Fortknox Vaults
* Escrow architecture
* Redemption contracts

### Stream C

Off-Chain Infrastructure

* Trusted Relayer
* Oracle Pool
* Commit-Reveal system
* Attestation network

The architecture is fully specified and buildable today. Estimated development effort is approximately 18–22 weeks to testnet using parallel development streams. 

---

# Milestones

## Milestone 1 – Core Infrastructure

Deliverables:

* qClaim token contract
* Qubic Factory Smart Contract
* 128-byte payload standard
* Fortknox Vault prototype

Success Criteria:

* Deposit and redemption simulation completed

---

## Milestone 2 – Dual-Witness Network

Deliverables:

* Trusted Relayer
* Oracle Pool
* Commit-Reveal committee selection
* Threshold attestation

Success Criteria:

* Successful dual-signature redemption flow

---

## Milestone 3 – QSWAP Integration

Deliverables:

* Wrapped asset issuance
* Liquidity pool deployment
* Governance integration

Success Criteria:

* Public testnet demonstration

---

## Milestone 4 – Security & Launch Readiness

Deliverables:

* Audit
* Monitoring
* Circuit breaker testing
* Recovery simulations

Success Criteria:

* Independent security review completed

---

# Return Proposal

The protocol creates value for Qubic through:

### Ecosystem Growth

New external liquidity entering Qubic.

### Increased Activity

More swaps, LP participation, governance, and smart contract interactions.

### Sustainable Revenue

Trading fees distributed across:

* Liquidity providers
* Smart Contract Shareholders
* Security and audit treasury 

### Long-Term Infrastructure Value

DWHAN is designed as foundational infrastructure rather than a speculative application.

Its success can be measured through:

* Total Value Locked (TVL)
* Wrapped asset supply
* Daily transaction volume
* QSWAP trading volume
* Active liquidity providers
* Redemption volume

---

## Vision

The Dual-Witness Hybrid Attestation Network aims to become the secure liquidity gateway for Qubic, replacing vulnerable bridge architectures with a shareholder-governed, escrow-backed model that prioritizes security, transparency, and sustainable ecosystem growth. 

## System Overview

<img width="1024" height="1536" alt="image" src="https://github.com/user-attachments/assets/969d0781-6953-440e-a4fd-a4b0cb73e8bc" />
