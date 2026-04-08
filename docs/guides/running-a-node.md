---
sidebar_position: 2
title: Running a Node
---

# Running a Node

This guide covers running a Quantus node for syncing, querying, and participating in the network. For mining-specific setup, see the [Mining Quickstart](./mining).

## Quick Start

```bash
# Download the latest release
# https://github.com/Quantus-Network/chain/releases/latest

# Generate node identity
./quantus-node key generate-node-key --file ~/.quantus/node_key.p2p

# Run a full sync node (non-mining)
./quantus-node \
    --chain dirac \
    --node-key-file ~/.quantus/node_key.p2p \
    --sync full
```

## Build from Source

```bash
# Install Rust nightly
rustup toolchain install nightly
rustup default nightly

# Clone and build
git clone https://github.com/Quantus-Network/chain.git
cd chain
cargo build --release

# Binary is at ./target/release/quantus-node
```

## Node Types

| Mode | Flag | Purpose |
|------|------|---------|
| Full Node | `--sync full` | Sync and validate the full chain. Serve RPC queries. |
| Validator (Miner) | `--validator` | Full node + actively mine blocks. See [Mining Quickstart](./mining). |

## Key Management

### Generate a Standard Address

```bash
# New key pair
./quantus-node key quantus

# Restore from 24-word mnemonic
./quantus-node key quantus --words "autumn bear crystal..."

# Restore from hex seed
./quantus-node key quantus --seed "<64-HEX-STRING>"
```

### Generate a Wormhole Address

```bash
./quantus-node key quantus --scheme wormhole
```

### Multiple Wallet Indices

```bash
# Account index 0 (default)
./quantus-node key quantus --wallet-index 0

# Account index 1
./quantus-node key quantus --wallet-index 1
```

Derivation path: `m/44'/189189'/{index}'/0'/0'`

## Local Development

### Single-Node Dev Chain

```bash
./quantus-node --dev
```

Runs a development chain with instant block production and pre-funded accounts.

### Multi-Node Local Testnet

```bash
# From workspace root
./scripts/run_local_nodes.sh
```

Launches two validator nodes and a listener node connected to each other.

## Docker Deployment

```bash
# Pull latest image
docker pull ghcr.io/quantus-network/quantus-node:latest

# Run full sync node
docker run -d \
  --name quantus-node \
  --restart unless-stopped \
  -v "$(pwd)/quantus_data":/var/lib/quantus \
  -p 30333:30333 \
  -p 9944:9944 \
  ghcr.io/quantus-network/quantus-node:latest \
  --base-path /var/lib/quantus \
  --chain dirac \
  --sync full
```

## RPC Endpoints

The JSON-RPC API is available on port 9944 by default.

```bash
# Get latest block
curl -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"chain_getBlock","params":[]}' \
  http://localhost:9944

# Get network state
curl -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"system_networkState","params":[]}' \
  http://localhost:9944
```

## Monitoring

### Prometheus Metrics

Available at `http://localhost:9616/metrics`. Key metrics:
- Block height
- Peer count
- Transaction pool size
- Mining difficulty (if validating)

### Debug Logging

```bash
# General debug
RUST_LOG=debug ./quantus-node [options]

# Consensus-specific debug
RUST_LOG=info,sc_consensus_pow=debug ./quantus-node [options]
```

## Network Ports

| Port | Protocol | Purpose | Expose? |
|------|----------|---------|---------|
| 30333 | TCP | P2P networking | Yes (required) |
| 9944 | TCP | JSON-RPC | Only if needed |
| 9616 | TCP | Prometheus metrics | Only for monitoring |
