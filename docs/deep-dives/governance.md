---
sidebar_position: 5
title: Governance
---

# Governance

Quantus uses the Polkadot OpenGov governance model with conviction voting, adapted for a proof-of-work chain. Governance enables forkless runtime upgrades -- critical for a chain that may need to swap cryptographic primitives as PQC standards evolve.

## Why Forkless Upgrades Matter

If NIST deprecates ML-DSA-87 or a vulnerability is found in Poseidon2, Quantus can upgrade its runtime via on-chain governance without coordinating a hard fork. The Substrate framework compiles the runtime to WASM, and governance can schedule a runtime swap that all nodes automatically adopt.

This is the key advantage over Bitcoin's upgrade model, where consensus changes require years of social coordination and miner signaling.

## Governance Structure

Quantus has a dual-track governance system:

### Community Governance

Open to all token holders. Uses **conviction voting** where the weight of a vote increases based on how long the voter locks their tokens:

| Lock Period | Vote Multiplier |
|-------------|----------------|
| No lock | 0.1x |
| 1x period | 1x |
| 2x period | 2x |
| 4x period | 3x |
| 8x period | 4x |
| 16x period | 5x |
| 32x period | 6x |

This prevents whale-dominated governance -- a small holder who locks for 32 periods has more vote weight than a whale who doesn't lock.

### Technical Collective

A smaller group of technically qualified members who can fast-track urgent protocol upgrades (security patches, critical bug fixes). Members are added/removed by community governance.

The Technical Collective has its own referendum track with lower thresholds and shorter voting periods, enabling rapid response to security issues without waiting for a full community vote.

## Governance Tracks

Referenda are submitted to different **tracks** based on their impact level:

| Track | Purpose | Approval Threshold | Typical Duration |
|-------|---------|-------------------|-----------------|
| Root | Runtime upgrades, parameter changes | Highest | Longest |
| Treasurer | Treasury spending proposals | High | Medium |
| General Admin | Non-critical administrative actions | Medium | Medium |
| Signaling | Non-binding community sentiment | Low | Short |

Each track has independent approval curves, minimum turnout requirements, and decision periods. Higher-impact tracks require more support and longer deliberation.

## Governance Components

| Pallet | Purpose |
|--------|---------|
| `pallet-referenda` | Community referendum management |
| `pallet-referenda::Instance1` | Technical Collective referenda |
| `pallet-conviction-voting` | Conviction-weighted vote tallying |
| `pallet-ranked-collective` | Technical Collective membership |
| `pallet-preimage` | Stores referendum proposal data |
| `pallet-scheduler` | Executes approved referenda at scheduled blocks |

## Key Source Code

| Component | Path |
|-----------|------|
| Governance track definitions | `runtime/src/governance/definitions.rs` |
| Runtime configuration | `runtime/src/configs/mod.rs` |
| Scheduler (custom) | `pallets/scheduler/` |
