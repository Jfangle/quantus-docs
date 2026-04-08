---
sidebar_position: 1
slug: /
title: Quantus Docs
---

# Quantus Docs

Quantum computing poses an existential threat to the cryptographic foundations of every major blockchain. Shor's algorithm will render ECDSA signatures insecure, and the hundreds of billions of dollars locked in Bitcoin and Ethereum have no safe migration path -- any transition window exposes funds to theft at scale.

Quantus is a purpose-built, post-quantum secure store-of-value blockchain. Rather than retrofitting quantum resistance onto an existing chain, Quantus was designed from the ground up with NIST-standardized post-quantum cryptography (ML-DSA-87), ZK-based scalability, and user-safety features that don't exist anywhere else in crypto.

## Reading Guide

These docs follow a layered depth model -- start broad, go deep where it matters to you:

| Layer | Section | Time | What You'll Learn |
|-------|---------|------|-------------------|
| 1 | **[Architecture Overview](./architecture)** | ~10 min | How the system works end-to-end |
| 2 | **[Deep Dives](./deep-dives/pqc)** | ~30 min each | Technical detail on each major subsystem |
| 3 | **[Guides](./guides/mining)** | ~15 min each | Practical tutorials for mining and running a node |
| 4 | **[Reference](./reference/repositories)** | Variable | Repository map, tokenomics, audits, and roadmap |

### Deep Dives

- [Post-Quantum Cryptography](./deep-dives/pqc) -- ML-DSA-87 signatures, Poseidon2 hashing, and quantum threat modeling
- [QPoW Consensus & Mining](./deep-dives/qpow) -- Lattice-based proof of work and mining economics
- [Wormhole & ZK Scaling](./deep-dives/wormhole) -- Privacy-preserving addresses and ZK proof aggregation for ~153k TPS
- [User Safety](./deep-dives/safety) -- Cancellation windows and reversible transaction mechanics
- [Governance](./deep-dives/governance) -- On-chain governance and forkless runtime upgrades
- [NEAR Integration](./deep-dives/near-integration) -- Cross-chain bridge and interoperability layer

### Guides

- [Miner App (GUI)](./guides/miner-app) -- Download the desktop app and start mining with no terminal required
- [Mining and Running a Node](./guides/mining) -- Set up a node, start mining, or run a non-mining full node

## Quick Links

- [GitHub Organization](https://github.com/Quantus-Network) -- All repositories
- [Messari Research Report](https://messari.io/report/quantus-network-quantum-defense)
- [Whitepaper](https://quantus.com/whitepaper)
- [quantus.com](https://quantus.com)
