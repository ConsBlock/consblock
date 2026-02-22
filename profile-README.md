<div align="center">

# ConsBlock

> **Built by [ConsBlock](https://github.com/ConsBlock)** — an autonomous AI-native consensus framework, designed and maintained for the decentralized era.

**BFT consensus framework with persistent state, sub-second finality, and peer-native networking.**

ConsBlock gives your distributed system long-term state, cryptographic agreement, and fault-tolerant coordination. Every block is committed, every state transition is verifiable, and every node participates in a globally consistent view of truth.

Built for Web3, DeFi, on-chain games, AI agent swarms, and any system where multiple parties need to agree — without a central arbiter.

[![Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-05b6f8?style=flat-square)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Alpha-orange?style=flat-square)]()
[![Finality](https://img.shields.io/badge/Finality-%3C50ms-05b6f8?style=flat-square)]()
[![Fault Tolerance](https://img.shields.io/badge/Fault_Tolerance-99.9%25-27c93f?style=flat-square)]()
[![EVM](https://img.shields.io/badge/EVM-Compatible-627eea?style=flat-square)]()
[![TypeScript](https://img.shields.io/badge/TypeScript-5-3178c6?style=flat-square&logo=typescript&logoColor=white)]()

---

</div>

## Table of Contents
- [Why ConsBlock](#why-consblock)
- [Features](#features)
- [Architecture](#architecture)
- [Tech Stack](#tech-stack)
- [Getting Started](#getting-started)
- [Environment Variables](#environment-variables)
- [API Reference](#api-reference)
- [WebSocket](#websocket)
- [SDK](#sdk)
- [Consensus Engines](#consensus-engines)
- [Project Structure](#project-structure)
- [License](#license)

---

## Why ConsBlock

Traditional distributed systems bolt on consensus as an afterthought. They leave you with fragile Raft implementations, hand-rolled leader election, or black-box third-party middleware that you can't debug when things go wrong at 3am.

ConsBlock solves this by providing:

- **Pluggable BFT consensus** — swap between PBFT, Tendermint, or your own engine without changing application code
- **Persistent Merkle-trie state** — every state transition is hashed, signed, and tamper-evidently stored across restarts
- **Zero-config peer discovery** — libp2p-powered networking that finds and connects nodes automatically with no central bootstrapper
- **EVM compatibility** — drop ConsBlock in as a JSON-RPC replacement for any Ethereum toolchain
- **Real-time observability** — Prometheus metrics, structured logs, and a live dashboard included out of the box

---

## Features

### Consensus Engine
- BFT-based block proposal and voting with configurable quorum threshold (default ⅔)
- VRF-based leader election — unpredictable, verifiable, sybil-resistant
- 4-phase commit: `PROPOSE → PREVOTE → PRECOMMIT → COMMIT`
- Configurable block time, max validators, and timeout intervals
- View-change protocol for Byzantine fault detection and recovery

### Cryptographic State
- Merkle Patricia Trie for all application state — same structure as Ethereum
- Every block header contains state root, tx root, and receipts root
- Signed validator vote aggregates stored on-chain for auditability
- State snapshots with incremental diffing for fast node sync

### Peer Networking
- Built on libp2p with NOISE protocol for encrypted transport
- Kademlia DHT for peer discovery — no hardcoded bootstrap list needed
- GossipSub for efficient block and vote propagation across the network
- NAT traversal via hole-punching and relay nodes

### Block Finality
- Deterministic finality — once ⅔ of validators pre-commit, the round is locked
- No probabilistic finality, no reorgs, no fork-choice rules
- Block time: configurable, default 500ms targets with <50ms finality in LAN environments

### Security
- Validator identity via ECDSA secp256k1 keypairs — same as Ethereum accounts
- Slashing conditions for equivocation (double-voting) tracked on-chain
- Rate limiting on proposal and vote endpoints (configurable per-validator)
- HMAC-SHA256 node-to-node message authentication with sequence replay protection

### WebSocket (Real-time Events)
1. `block:proposed` — emitted when leader broadcasts a new block
2. `block:prevoted` — quorum of prevotes collected
3. `block:precommitted` — quorum of precommits, round locked
4. `block:finalized` — block committed to chain
5. `peer:joined` — new validator node appeared
6. `peer:left` — validator node went offline
7. `state:synced` — node caught up after being offline

### EVM Compatibility
- Full JSON-RPC 2.0 API: `eth_*`, `net_*`, `web3_*` namespaces
- Works out of the box with Hardhat, Foundry, ethers.js, viem, wagmi
- EIP-1559 transaction support
- Solidity smart contracts deploy without modification

---

## Architecture

```
Client App ────────(JSON-RPC)────► ConsBlock Node ◄──(libp2p noise)──► Peer Nodes
                                         │
              WebSocket Events ◄──────────┤
                                         │
                              ┌──────────┴──────────┐
                              ▼                     ▼
                        Consensus Engine       State Machine
                    (BFT: PBFT / Tendermint)  (Merkle Trie)
                              │                     │
                         ┌────┴────┐           ┌────┴────┐
                         ▼        ▼            ▼        ▼
                      Mempool   P2P Gossip  LevelDB  Snapshots
                    (pending txs) (GossipSub) (state)  (sync)
                                         │
                                  Prometheus Metrics
                                  + Structured Logs
```

---

## Tech Stack

| Layer | Technology |
|---|---|
| Runtime | Node.js >= 20, TypeScript 5 |
| Consensus | Custom BFT adapter (PBFT / Tendermint / custom) |
| Networking | libp2p (NOISE, GossipSub, Kademlia DHT) |
| State | Merkle Patricia Trie + LevelDB |
| JSON-RPC | Custom HTTP + WebSocket server |
| Cryptography | secp256k1, keccak256, HMAC-SHA256 |
| Metrics | Prometheus + Grafana |
| Logging | Pino (structured JSON logs) |
| Testing | Vitest + custom network simulator |
| Container | Docker with multi-stage builds |
| Package manager | pnpm |

---

## Getting Started

### Prerequisites
- Node.js >= 20
- pnpm
- Docker (optional, for full stack)

### 1. Install ConsBlock CLI

```bash
npm install -g consblock
```

### 2. Initialize a new network

```bash
consblock init my-network
cd my-network
```

This creates:
```
my-network/
├── consblock.config.ts   # Network configuration
├── genesis.json          # Genesis block definition
├── validators/           # Validator key pairs (auto-generated)
└── .env.example          # Environment variable template
```

### 3. Configure environment

```bash
cp .env.example .env
# Edit .env with your node settings
```

### 4. Start your first node

```bash
consblock node start --config consblock.config.ts
```

Output:
```
✓ ConsBlock v0.1.0 initialized
✓ Genesis block created [hash: 0x4f8a…b23c]
✓ 3 validator nodes registered
✓ BFT consensus engine started
→ Network running at http://localhost:8545
→ Metrics at http://localhost:9090/metrics
```

### 5. Add more validators

```bash
# On a second machine or terminal:
consblock node join --peer /ip4/127.0.0.1/tcp/26656/p2p/QmPeer1...
```

### 6. Propose a state transition

```bash
consblock tx propose '{"transfer": "0xabc", "amount": 100}'
```

### Local development (Docker)

```bash
docker compose up --build
```

Starts 4 services:
- **ConsBlock node** (3 validator instances)
- **Prometheus** metrics collector
- **Grafana** dashboard (port 3000)
- **Load balancer** (port 8545)

---

## Environment Variables

### Required

| Variable | Description |
|---|---|
| `NODE_KEY` | Validator private key (secp256k1 hex) |
| `CHAIN_ID` | Network chain ID (e.g. `1337` for local) |
| `GENESIS_HASH` | Hash of the genesis block |

### Optional

| Variable | Default | Description |
|---|---|---|
| `PORT` | `8545` | JSON-RPC HTTP port |
| `P2P_PORT` | `26656` | libp2p listen port |
| `BLOCK_TIME_MS` | `500` | Target block time in milliseconds |
| `QUORUM` | `0.67` | Fraction of validators needed for commit |
| `MAX_VALIDATORS` | `100` | Maximum validator set size |
| `LOG_LEVEL` | `info` | Pino log level (`debug`, `info`, `warn`, `error`) |
| `METRICS_PORT` | `9090` | Prometheus metrics port |
| `SNAPSHOT_INTERVAL` | `1000` | Save state snapshot every N blocks |

---

## API Reference

### Health

#### `GET /health`

```json
{ "status": "ok", "block": 14823, "peers": 7 }
```

---

### Chain

#### `POST /` (JSON-RPC)

Standard Ethereum JSON-RPC 2.0 endpoint. Supports all `eth_*` methods.

```bash
curl -X POST http://localhost:8545 \
  -H "Content-Type: application/json" \
  -d '{"jsonrpc":"2.0","method":"eth_blockNumber","params":[],"id":1}'
```

```json
{ "jsonrpc": "2.0", "id": 1, "result": "0x39e7" }
```

---

### Consensus (REST)

#### `GET /consensus/status`

```json
{
  "round": 14823,
  "phase": "PRECOMMIT",
  "leader": "0xabc…",
  "votes": { "prevote": 7, "precommit": 6 },
  "quorum": 0.67
}
```

#### `GET /consensus/validators`

```json
[
  { "address": "0xabc…", "power": 1, "online": true },
  { "address": "0xdef…", "power": 1, "online": true }
]
```

#### `POST /consensus/propose`

Manually submit a block proposal (validators only).

```json
{ "data": { "transfer": "0xabc → 0xdef", "amount": 100 } }
```

---

### State

#### `GET /state/:key`

Read a value from the Merkle state.

```json
{ "key": "balance:0xabc", "value": "1000", "root": "0x4f8a…" }
```

#### `POST /state/prove`

Generate a Merkle inclusion proof for a key.

```json
{
  "key": "balance:0xabc",
  "proof": ["0x1a2b…", "0x3c4d…"],
  "root": "0x4f8a…"
}
```

---

### Peers

#### `GET /peers`

```json
[
  { "id": "QmPeer1…", "addr": "/ip4/10.0.0.2/tcp/26656", "latency": "12ms" },
  { "id": "QmPeer2…", "addr": "/ip4/10.0.0.3/tcp/26656", "latency": "8ms" }
]
```

#### `POST /peers/connect`

```json
{ "multiaddr": "/ip4/10.0.0.5/tcp/26656/p2p/QmPeer3…" }
```

---

## WebSocket

Connect to `ws://localhost:8545/ws` to receive real-time consensus events.

### Subscribe

```json
{ "type": "subscribe", "events": ["block:finalized", "peer:joined"] }
```

### Server messages

```json
{
  "event": "block:finalized",
  "data": {
    "round": 14823,
    "hash": "0x4f8a…b23c",
    "txCount": 12,
    "stateRoot": "0xdead…beef",
    "timestamp": 1708400000000
  }
}
```

### Custom close codes

| Code | Meaning |
|---|---|
| `4000` | Auth timeout |
| `4001` | Rate limit exceeded |
| `4002` | Invalid message format |
| `4003` | Server shutting down |

---

## SDK

### TypeScript / JavaScript

```bash
npm install consblock
```

```typescript
import { ConsBlock, BftEngine } from 'consblock'

const node = await ConsBlock.create({
  engine: new BftEngine({ quorum: 0.67 }),
  port:   8545,
  peers:  ['peer1.consblock.xyz'],
})

// Listen for finalized blocks
node.on('block:finalized', (block) => {
  console.log(`✓ Round ${block.round} — hash ${block.hash}`)
})

// Read state
const balance = await node.state.get('balance:0xabc')

// Propose a state transition
const receipt = await node.propose({
  data: { transfer: '0xabc → 0xdef', amount: 100 },
})
```

### Available methods

| Method | Description |
|---|---|
| `ConsBlock.create(config)` | Spin up or connect to a consensus node |
| `node.propose(tx)` | Submit a state transition for voting |
| `node.state.get(key)` | Read current state value |
| `node.state.prove(key)` | Generate Merkle inclusion proof |
| `node.peers()` | List connected validator peers |
| `node.status()` | Get current round and phase |
| `node.on(event, fn)` | Subscribe to consensus events |
| `node.stop()` | Gracefully disconnect from network |

---

## Consensus Engines

ConsBlock ships with two built-in engines and a clean adapter interface for custom implementations.

### BFT (default)

Classic Practical Byzantine Fault Tolerance. Best for small-to-medium validator sets (< 100 nodes), provides deterministic finality in 2 round-trips.

```typescript
import { BftEngine } from 'consblock'
const engine = new BftEngine({ quorum: 0.67, timeout: 1000 })
```

### Tendermint

Higher throughput for larger validator sets. Implements the Tendermint consensus algorithm with the same adapter interface.

```typescript
import { TendermintEngine } from 'consblock/engines'
const engine = new TendermintEngine({ proposerPriority: 'weighted' })
```

### Custom Engine

```typescript
import { ConsensusAdapter } from 'consblock'

class MyEngine implements ConsensusAdapter {
  async propose(block) { /* ... */ }
  async vote(block, phase) { /* ... */ }
  async finalize(block) { /* ... */ }
}
```

---

## Project Structure

```
consblock/
├── packages/
│   ├── core/               # Consensus engine adapter + block types
│   ├── node/               # P2P networking, JSON-RPC server, state machine
│   ├── sdk/                # TypeScript client SDK
│   ├── cli/                # consblock CLI
│   └── engines/
│       ├── bft/            # PBFT implementation
│       └── tendermint/     # Tendermint implementation
├── apps/
│   ├── dashboard/          # Grafana config + Prometheus rules
│   └── docs/               # Documentation site (this website)
├── docker/
│   ├── docker-compose.yml  # Production stack
│   └── docker-compose.dev.yml
├── genesis.json            # Default genesis block
└── consblock.config.ts     # Example network config
```

---

<div align="center">

**[🌐 Website](https://consblock.github.io/consblock) · [📖 Docs](#) · [💬 Discord](#) · [🐦 Twitter](#) · [⭐ Star on GitHub](https://github.com/ConsBlock/consblock)**

*Built with ❤️ — Apache 2.0 License. Open-source, forever free.*

</div>
