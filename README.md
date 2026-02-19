<div align="center">

<img src="https://img.shields.io/badge/ConsBlock-v0.1.0-05b6f8?style=for-the-badge&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHZpZXdCb3g9IjAgMCAyNCAyNCI+PHBhdGggZmlsbD0id2hpdGUiIGQ9Im0xMyAzLTguNSA5LjVIMTJsLTEgOC41IDguNS05LjVIMTJaIi8+PC9zdmc+" alt="ConsBlock"/>

# ConsBlock

### **Build consensus at the speed of thought.**

*Open-source BFT consensus framework for distributed systems & Web3*

[![Apache 2.0](https://img.shields.io/badge/License-Apache_2.0-05b6f8?style=flat-square)](LICENSE)
[![Status](https://img.shields.io/badge/Status-Alpha-orange?style=flat-square)]()
[![Finality](https://img.shields.io/badge/Finality-%3C50ms-05b6f8?style=flat-square)]()
[![Fault Tolerance](https://img.shields.io/badge/Fault_Tolerance-99.9%25-27c93f?style=flat-square)]()

```bash
npx consblock init my-network
```

</div>

---

## 🧩 What is ConsBlock?

ConsBlock is a **production-grade consensus SDK** that gives your distributed system persistent state and fault-tolerant agreement — without the protocol complexity.

Whether you're building a DeFi protocol, an on-chain game, or an AI agent swarm, ConsBlock gives you the coordination primitives you need.

---

## ⚡ Features

| Feature | Description |
|---|---|
| 🔋 **Sub-second Finality** | BFT engine delivers `<50ms` block finality, even under adversarial conditions |
| 🔐 **Cryptographic State** | Every transition is signed, hashed & Merkle-trie verified |
| 🌐 **Peer Discovery** | libp2p-powered. Nodes find each other automatically — no bootstrapper needed |
| 🔄 **Hot-swap Engines** | Swap between PBFT, Tendermint, or custom consensus at runtime |
| 🧩 **EVM Compatible** | Drop-in JSON-RPC replacement for any EVM chain |
| 📊 **Observability** | Prometheus metrics + structured logs built in |

---

## 🚀 Quick Start

```typescript
import { ConsBlock, BftEngine } from 'consblock'

// Spin up a consensus node
const node = await ConsBlock.create({
  engine: new BftEngine({ quorum: 0.67 }),
  port:   8545,
  peers:  ['peer1.consblock.xyz'],
})

// Listen for finalized blocks
node.on('block:finalized', (block) => {
  console.log(`✓ Round ${block.round} finalized`)
})

// Propose a state transition
await node.propose({
  data: { transfer: '0xabc → 0xdef', amount: 100 },
})
```

---

## 🛠 Use Cases

- **DeFi Protocols** — cryptographic finality, no forks, no double-spends
- **On-chain Games** — millisecond game-state sync across players
- **AI Agent Swarms** — coordinate agents on shared observations
- **Supply Chain** — immutable audit trails with multi-party sign-off
- **DAOs & Governance** — tamper-proof voting with verifiable tally
- **Cross-chain Bridges** — BFT relayer consensus for trustless messaging

---

## 📦 Repositories

| Repo | Description |
|---|---|
| [`consblock`](https://github.com/ConsBlock/consblock) | 🌐 Landing page & documentation site |

---

<div align="center">

**[🌐 Website](https://consblock.github.io/consblock) · [📖 Docs](#) · [💬 Discord](#) · [🐦 Twitter](#)**

*Built with ❤️ and Apache 2.0 — open source, forever free.*

</div>
