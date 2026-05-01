# Yo Network

<div align="center">

![Yo Network](https://explorer.yonetwork.org/yonetwork-logo-transparent.png)

**A high-performance EVM-compatible blockchain built on Cosmos SDK + CometBFT**

[![License](https://img.shields.io/badge/License-ENCL--1.0-blue.svg)](LICENSE)
[![Go Version](https://img.shields.io/badge/Go-1.21+-00ADD8.svg)](https://golang.org/)
[![Chain ID](https://img.shields.io/badge/Chain%20ID-9000-green.svg)](https://explorer.yonetwork.org)
[![Explorer](https://img.shields.io/badge/Explorer-Live-brightgreen.svg)](https://explorer.yonetwork.org)

[Website](https://yonetwork.org) • [Explorer](https://explorer.yonetwork.org) • [Documentation](#) • [Telegram](https://t.me/certitude_cars)

</div>

---

## Overview

Yo Network is a next-generation EVM-compatible blockchain that combines the security and interoperability of the Cosmos ecosystem with the developer familiarity of the Ethereum Virtual Machine.

Built as a fork of [Evmos](https://evmos.org), Yo Network delivers:

- ⚡ **Fast finality** — ~3 second block times
- 🔗 **Full EVM compatibility** — deploy any Solidity smart contract
- 🌐 **IBC-enabled** — interoperable with 100+ Cosmos chains
- 🛡️ **Proof-of-Stake** security via CometBFT consensus
- 💎 **Native token: YO** — used for gas, staking, and governance

---

## Network Details

| Parameter | Value |
|-----------|-------|
| **Network Name** | Yo Network |
| **Native Token** | YO |
| **Token Decimals** | 18 |
| **Chain ID (EVM)** | 9000 |
| **Chain ID (Cosmos)** | evmos_9000-1 |
| **Block Time** | ~3 seconds |
| **Consensus** | CometBFT (BFT PoS) |
| **Total Supply** | 5,000,000,000 YO |
| **EVM Compatible** | Yes (Solidity, Vyper) |
| **IBC Compatible** | Yes |

---

## Network Endpoints

| Service | URL |
|---------|-----|
| **RPC (EVM/JSON-RPC)** | https://rpc.yonetwork.org |
| **WebSocket** | wss://ws.yonetwork.org |
| **Cosmos REST API** | https://api.yonetwork.org |
| **Block Explorer** | https://explorer.yonetwork.org |

---

## Add to MetaMask

| Field | Value |
|-------|-------|
| Network Name | Yo Network |
| RPC URL | https://rpc.yonetwork.org |
| Chain ID | 9000 |
| Currency Symbol | YO |
| Block Explorer URL | https://explorer.yonetwork.org |

---

## Token Information

| Field | Value |
|-------|-------|
| **Token Name** | YO |
| **Token Symbol** | YO |
| **Decimals** | 18 |
| **Total Supply** | 5,000,000,000 YO |
| **Base Denomination** | aevmos (1 YO = 10^18 aevmos) |
| **Type** | Native Gas + Governance Token |
| **Staking** | Yes — earn rewards by delegating to validators |
| **Governance** | Yes — vote on protocol upgrades |

---

## Architecture

```
┌─────────────────────────────────────────────────────┐
│                    Yo Network                        │
│                                                     │
│  ┌───────────────┐    ┌───────────────────────────┐ │
│  │  EVM Layer    │    │    Cosmos SDK Modules      │ │
│  │  (Ethermint)  │    │  Bank, Staking, Gov, IBC   │ │
│  └───────┬───────┘    └────────────┬──────────────┘ │
│          │                         │                 │
│  ┌───────▼─────────────────────────▼──────────────┐ │
│  │            CometBFT Consensus Engine            │ │
│  │         (Byzantine Fault Tolerant PoS)          │ │
│  └─────────────────────────────────────────────────┘ │
└─────────────────────────────────────────────────────┘
```

**Key components:**
- **Ethermint** — EVM implementation for Cosmos SDK
- **CometBFT** — Production-grade BFT consensus
- **Cosmos SDK v0.47** — Modular blockchain framework
- **IBC** — Inter-Blockchain Communication protocol

---

## Getting Started

### Prerequisites

- Go 1.21+
- Git
- Make

### Quick Start

```bash
# Clone the repository
git clone https://github.com/yonetwork25/YoNetwork.git
cd YoNetwork

# Build the binary
make install

# Verify installation
evmosd version
```

For full node setup, validator setup, and developer guides, see [INSTALL.md](INSTALL.md).

---

## Validators

Yo Network is secured by a set of Proof-of-Stake validators. Current active validators:

| Validator | Stake | Status |
|-----------|-------|--------|
| yo-node-1 | 1,000,000 YO | Active |
| YoClub | 9,990,000 YO | Active |

**Become a validator** — see [INSTALL.md](INSTALL.md#validator-setup) for instructions.

---

## Smart Contract Development

Yo Network is fully EVM-compatible. Deploy your existing Ethereum contracts with no changes:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract HelloYoNetwork {
    string public message = "Hello, Yo Network!";
    
    function setMessage(string memory _msg) public {
        message = _msg;
    }
}
```

**Deploy with:**
- [Remix IDE](https://remix.ethereum.org) — point to https://rpc.yonetwork.org
- [Hardhat](https://hardhat.org) — Chain ID 9000
- [Foundry](https://getfoundry.sh) — fully compatible

---

## Governance

YO token holders can participate in on-chain governance:

```bash
# Submit a proposal
evmosd tx gov submit-proposal [proposal-file] --from [key] --chain-id evmos_9000-1

# Vote on a proposal
evmosd tx gov vote [proposal-id] yes --from [key] --chain-id evmos_9000-1

# Query proposals
evmosd query gov proposals
```

---

## Staking

Earn YO rewards by delegating to validators:

```bash
# Delegate YO to a validator
evmosd tx staking delegate [validator-address] [amount]aevmos \
  --from [your-key] \
  --chain-id evmos_9000-1 \
  --gas auto \
  --gas-prices 1000000000aevmos

# Claim staking rewards
evmosd tx distribution withdraw-all-rewards \
  --from [your-key] \
  --chain-id evmos_9000-1
```

---

## IBC

Yo Network supports IBC for cross-chain transfers:

```bash
# IBC transfer
evmosd tx ibc-transfer transfer transfer [channel-id] [receiver] [amount]aevmos \
  --from [key] \
  --chain-id evmos_9000-1
```

---

## Repository Structure

```
YoNetwork/
├── app/                    # Application setup and modules
├── cmd/evmosd/            # CLI entry point
├── contracts/             # Pre-compiled Solidity contracts
├── crypto/                # Cryptographic utilities
├── precompiles/           # EVM precompiles
├── proto/                 # Protocol buffer definitions
├── server/                # Node server implementation
├── testutil/              # Testing utilities
├── types/                 # Core types
├── utils/                 # Utility functions
├── x/                     # Cosmos SDK modules
│   ├── erc20/            # ERC-20 module
│   ├── evm/              # EVM module
│   ├── feemarket/        # EIP-1559 fee market
│   ├── inflation/        # Token inflation
│   └── revenue/          # Developer revenue sharing
├── INSTALL.md
├── LISTING.md
└── README.md
```

---

## Contributing

We welcome contributions from the community!

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

---

## Security

Found a security vulnerability? Please **do not** open a public issue.

Contact us privately:
- **Email:** contact@yonetwork.org
- **Telegram:** https://t.me/certitude_cars

---

## Links

| Resource | URL |
|----------|-----|
| Website | https://yonetwork.org |
| Explorer | https://explorer.yonetwork.org |
| RPC | https://rpc.yonetwork.org |
| Telegram | https://t.me/yonetwork25 |
| Email | contact@yonetwork.org |

---

## License

Yo Network is licensed under the [Evmos Non-Commercial License 1.0 (ENCL-1.0)](LICENSE). Some files are licensed under [LGPLv3](https://www.gnu.org/licenses/lgpl-3.0.html).

---

<div align="center">
Built with ❤️ by the Yo Network team
</div>
