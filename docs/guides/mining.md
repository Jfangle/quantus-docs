---
sidebar_position: 1
title: Mining Quickstart
---

# Mining Quickstart

Get mining on the Quantus Network testnet in minutes. This guide covers binary installation, Docker setup, and external miner configuration.

## Prerequisites

| Requirement | Minimum | Recommended |
|------------|---------|-------------|
| CPU | 2+ cores | 4+ cores |
| RAM | 4 GB | 8 GB+ |
| Storage | 100 GB | 500 GB+ SSD |
| Network | Stable internet | 10+ Mbps broadband |
| OS | Linux (Ubuntu 20.04+), macOS 10.15+, or Windows WSL2 | Linux recommended |

## Option 1: Binary Installation

### Step 1: Download

Get the latest binary from [GitHub Releases](https://github.com/Quantus-Network/chain/releases/latest).

Or build from source:

```bash
# Install Rust nightly
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
rustup toolchain install nightly
rustup default nightly

# Verify (need nightly 12-24 or newer)
cargo --version

# Clone and build
git clone https://github.com/Quantus-Network/chain.git
cd chain
cargo build --release
```

### Step 2: Generate Node Identity

```bash
./quantus-node key generate-node-key --file ~/.quantus/node_key.p2p
```

### Step 3: Generate Wormhole Address

All mining rewards are sent to a **wormhole address** derived from your preimage. This is mandatory -- it ensures mining rewards are privacy-preserving by default.

```bash
./quantus-node key quantus --scheme wormhole
```

This outputs:
- **Address**: Your wormhole address (where rewards go)
- **inner_hash**: Your 32-byte preimage (use this to start mining)

**Save both values securely.** You need the `inner_hash` for the `--rewards-preimage` parameter.

### Step 4: Start Mining

```bash
./quantus-node \
    --validator \
    --chain dirac \
    --node-key-file ~/.quantus/node_key.p2p \
    --rewards-preimage <YOUR_INNER_HASH> \
    --max-blocks-per-request 64 \
    --sync full
```

Your node will sync with the network and begin mining automatically.

## Option 2: Docker Installation

### Step 1: Prepare Data Directory

```bash
mkdir -p ./quantus_node_data
```

### Step 2: Generate Node Identity

```bash
docker run --rm \
  -v "$(pwd)/quantus_node_data":/var/lib/quantus_data \
  ghcr.io/quantus-network/quantus-node:latest \
  key generate-node-key --file /var/lib/quantus_data/node_key.p2p
```

> **Apple Silicon (M1/M2/M3):** Add `--platform linux/amd64` to all Docker commands.

### Step 3: Generate Wormhole Address

```bash
docker run --rm ghcr.io/quantus-network/quantus-node:latest key quantus --scheme wormhole
```

Save the `inner_hash` value.

### Step 4: Run the Node

```bash
docker run -d \
  --name quantus-node \
  --restart unless-stopped \
  -v "$(pwd)/quantus_node_data":/var/lib/quantus \
  -p 30333:30333 \
  -p 9944:9944 \
  ghcr.io/quantus-network/quantus-node:latest \
  --validator \
  --base-path /var/lib/quantus \
  --chain dirac \
  --node-key-file /var/lib/quantus/node_key.p2p \
  --rewards-preimage <YOUR_INNER_HASH>
```

### Docker Management

```bash
# View logs
docker logs -f quantus-node

# Stop
docker stop quantus-node

# Start again
docker start quantus-node

# Update to latest version
docker stop quantus-node && docker rm quantus-node
docker pull ghcr.io/quantus-network/quantus-node:latest
# Re-run the docker run command above (data is preserved)
```

## External Miner (Higher Performance)

For better mining performance, offload the PoW computation to a dedicated miner process:

### Step 1: Build the External Miner

```bash
git clone https://github.com/Quantus-Network/quantus-miner
cd quantus-miner
cargo build --release
```

### Step 2: Start the Miner

```bash
RUST_LOG=info ./target/release/quantus-miner
```

Default API endpoint: `http://127.0.0.1:9833`

### Step 3: Start the Node with External Miner

```bash
RUST_LOG=info ./quantus-node \
    --validator \
    --chain dirac \
    --external-miner-url http://127.0.0.1:9833 \
    --rewards-preimage <YOUR_INNER_HASH>
```

## Verify Mining is Working

### Check Logs

Look for lines like:
```
Imported #12345 (0xabc...)
```

This means your node is syncing and importing blocks.

### Check Balance

```bash
curl -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","id":1,"method":"faucet_getAccountInfo","params":["YOUR_WORMHOLE_ADDRESS"]}' \
  http://localhost:9944
```

### Prometheus Metrics

Visit `http://localhost:9616/metrics` for detailed node metrics.

## Configuration Reference

| Parameter | Description | Default |
|-----------|-------------|---------|
| `--validator` | Enable mining | Required |
| `--chain` | Chain specification | `dirac` (testnet) |
| `--node-key-file` | P2P identity file | Required |
| `--rewards-preimage` | Wormhole preimage for rewards | Required |
| `--external-miner-url` | External miner API endpoint | None (uses local mining) |
| `--port` | P2P networking port | 30333 |
| `--rpc-port` | JSON-RPC port | 9944 |
| `--prometheus-port` | Metrics port | 9616 |
| `--base-path` | Data directory | `~/.local/share/quantus-node` |
| `--max-blocks-per-request` | Sync batch size | 64 |

## Troubleshooting

| Problem | Solution |
|---------|----------|
| Port already in use | Use `--port 30334 --rpc-port 9945` |
| Database corruption | Run `quantus-node purge-chain --chain dirac` |
| Mining not working | Verify `--validator` flag is present |
| Can't find rewards | Check the `Address` field from wormhole key generation |
| Wrong wormhole address | Use the exact `inner_hash` from key generation |
| Connection issues | Check firewall allows port 30333 |

## Testnet Information

| Property | Value |
|----------|-------|
| Chain | Dirac Testnet |
| Consensus | QPoW (Poseidon2 PoW) |
| Block Time | ~12 seconds target |
| Tokens | No monetary value (testnet) |
| Faucet | See [Telegram](https://t.me/quantusnetwork) |

## Security Notes

- **Back up your wormhole key pair** (address + inner_hash) in encrypted storage
- **Back up your node identity key** (`node_key.p2p`)
- Only expose necessary ports (30333 for P2P; keep 9944 behind a firewall unless you need remote RPC)
- Keep your node binary updated to the latest release
