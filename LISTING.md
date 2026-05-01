# Yo Network — Exchange Listing Document

**Version:** 1.0  
**Date:** April 2026  
**Prepared by:** Yo Network Team

---

## Table of Contents

1. [Project Overview](#1-project-overview)
2. [Token Information](#2-token-information)
3. [Network Specifications](#3-network-specifications)
4. [Technical Architecture](#4-technical-architecture)
5. [Public Endpoints](#5-public-endpoints)
6. [Wallet Integration](#6-wallet-integration)
7. [Smart Contract Details](#7-smart-contract-details)
8. [Tokenomics](#8-tokenomics)
9. [Security & Audits](#9-security--audits)
10. [Team & Governance](#10-team--governance)
11. [Roadmap](#11-roadmap)
12. [Legal & Compliance](#12-legal--compliance)
13. [Exchange Integration Guide](#13-exchange-integration-guide)
14. [Contact Information](#14-contact-information)

---

## 1. Project Overview

**Project Name:** Yo Network  
**Ticker Symbol:** YO  
**Category:** Layer 1 Blockchain, EVM-Compatible  
**Website:** https://yonetwork.org  
**Explorer:** https://explorer.yonetwork.org  
**GitHub:** https://github.com/yonetwork25/YoNetwork  
**Whitepaper:** https://yonetwork.org/whitepaper  

### Description

Yo Network is a high-performance, EVM-compatible Layer 1 blockchain built on the Cosmos SDK with CometBFT consensus. It enables developers to deploy Ethereum-compatible smart contracts while benefiting from the speed, security, and interoperability of the Cosmos ecosystem.

Yo Network bridges two of the most powerful blockchain ecosystems — Ethereum and Cosmos — providing users and developers with a seamless, fast, and low-cost experience. The native token YO serves as the primary medium for gas fees, staking rewards, and on-chain governance.

### Key Value Propositions

- **EVM Compatibility** — Run any Solidity smart contract without modification
- **Fast Finality** — ~3 second block times with instant transaction finality
- **Low Fees** — Significantly lower gas costs than Ethereum mainnet
- **IBC Interoperability** — Native cross-chain communication with 100+ Cosmos chains
- **Proof-of-Stake** — Energy-efficient consensus with staking rewards for YO holders
- **Decentralized Governance** — YO holders vote on all protocol changes

---

## 2. Token Information

| Field | Value |
|-------|-------|
| **Token Name** | YO |
| **Token Symbol** | YO |
| **Token Type** | Native Layer 1 Gas Token |
| **Total Supply** | 5,000,000,000 YO |
| **Circulating Supply** | Contact team for current figure |
| **Decimals** | 18 |
| **Base Denomination** | aevmos (1 YO = 10^18 aevmos) |
| **Blockchain** | Yo Network (evmos_9000-1) |
| **Chain ID (EVM)** | 9000 |
| **Standard** | Native (not ERC-20 on its own chain) |
| **Mintable** | Yes (via inflation module — see Tokenomics) |
| **Burnable** | Yes (gas fees are partially burned) |
| **Staking** | Yes |
| **Governance** | Yes |

---

## 3. Network Specifications

| Parameter | Value |
|-----------|-------|
| **Network Name** | Yo Network |
| **Chain ID (Cosmos)** | evmos_9000-1 |
| **Chain ID (EVM)** | 9000 |
| **Block Time** | ~3 seconds |
| **Block Size** | 4,294,967,295 gas |
| **Consensus Algorithm** | CometBFT (BFT Proof-of-Stake) |
| **Finality** | Instant (single-slot) |
| **Node Software** | evmosd v16.0.4 |
| **Go Version** | 1.21+ |
| **P2P Port** | 26656 |
| **RPC Port** | 26657 (Tendermint) / 8545 (EVM JSON-RPC) |
| **WebSocket Port** | 8546 |
| **REST API Port** | 1317 |
| **EVM Compatibility** | Ethereum London (EIP-1559) |
| **IBC Version** | IBC v7 |

---

## 4. Technical Architecture

### Consensus Layer
Yo Network uses **CometBFT** (formerly Tendermint), a Byzantine Fault Tolerant (BFT) consensus engine that provides:
- Instant transaction finality
- Resistance to up to 1/3 of faulty validators
- Deterministic block production

### Execution Layer
The **Ethermint** EVM module provides full Ethereum Virtual Machine compatibility:
- Support for all EVM opcodes
- EIP-1559 fee market
- Ethereum JSON-RPC API (eth_, net_, web3_, personal_ namespaces)
- Compatible with all Ethereum tooling (MetaMask, Hardhat, Foundry, Remix)

### Application Layer
Built on **Cosmos SDK v0.47**, including:
- `x/bank` — Token transfers
- `x/staking` — Validator management
- `x/gov` — On-chain governance
- `x/distribution` — Staking reward distribution
- `x/slashing` — Validator accountability
- `x/evm` — EVM execution
- `x/erc20` — ERC-20 ↔ native token bridge
- `x/ibc` — Cross-chain communication

### Interoperability
- **IBC (Inter-Blockchain Communication)** — Transfer tokens and data between Cosmos chains
- **ERC-20 Module** — Seamlessly convert native Cosmos tokens to ERC-20 and back

---

## 5. Public Endpoints

### Mainnet

| Endpoint Type | URL | Protocol |
|--------------|-----|----------|
| EVM JSON-RPC | https://rpc.yonetwork.org | HTTPS |
| EVM WebSocket | wss://ws.yonetwork.org | WSS |
| Cosmos REST API | https://api.yonetwork.org | HTTPS |
| Tendermint RPC | https://rpc.yonetwork.org:26657 | HTTPS |
| Block Explorer | https://explorer.yonetwork.org | HTTPS |

### Endpoint Status
All endpoints are monitored 24/7 with 99.9% uptime SLA target.

---

## 6. Wallet Integration

### EVM Wallets (MetaMask, Trust Wallet, etc.)

```
Network Name: Yo Network
RPC URL: https://rpc.yonetwork.org
Chain ID: 9000
Currency Symbol: YO
Block Explorer URL: https://explorer.yonetwork.org
```

### Cosmos Wallets (Keplr, Leap, Cosmostation)

```
Chain ID: evmos_9000-1
Chain Name: Yo Network
RPC: https://rpc.yonetwork.org:26657
REST: https://api.yonetwork.org
Bech32 Prefix: evmos
Coin Denom: aevmos
Coin Minimal Denom: aevmos
Coin Decimals: 18
Coin Symbol: YO
```

### Address Format
- **EVM Address:** `0x` prefixed, 20 bytes (standard Ethereum format)
- **Cosmos Address:** `evmos1` prefixed, bech32 encoded
- Both address formats refer to the same account (derived from same private key)

### Address Conversion

```javascript
// Convert between EVM and Cosmos address formats using ethermint tools
// EVM: 0x1234...
// Cosmos: evmos1...
// They map to the same private key
```

---

## 7. Smart Contract Details

### EVM Precompiles

Yo Network includes custom precompiled contracts at:

| Address | Function |
|---------|----------|
| `0x0000000000000000000000000000000000000100` | Staking precompile |
| `0x0000000000000000000000000000000000000400` | Distribution precompile |
| `0x0000000000000000000000000000000000000800` | IBC transfer precompile |

### Deploy Smart Contracts

```javascript
// hardhat.config.js
module.exports = {
  networks: {
    yonetwork: {
      url: "https://rpc.yonetwork.org",
      chainId: 9000,
      accounts: [process.env.PRIVATE_KEY]
    }
  }
}
```

---

## 8. Tokenomics

### Supply Distribution

| Allocation | Amount (YO) | Percentage | Details |
|------------|-------------|------------|---------|
| **Initial Supply** | 5,000,000,000 | 100% | Genesis allocation |
| **Validator Staking** | ~11,000,000 | 0.22% | Currently staked |
| **Inflation** | Variable | — | See schedule below |

### Inflation Model

Yo Network uses an exponential decay inflation model:

| Parameter | Value |
|-----------|-------|
| Initial Inflation | ~300M YO/year |
| Target Bonding Ratio | 66% |
| Decay Rate | 50% per period |
| Inflation Distribution | 53.3% Staking Rewards / 46.7% Community Pool |

### Fee Model

- **Gas Token:** YO (aevmos denomination)
- **Base Fee:** EIP-1559 dynamic fee model
- **Min Gas Price:** 0.0025 aevmos
- **Fee Burn:** Portion of base fees burned (deflationary pressure)

---

## 9. Security & Audits

### Consensus Security
- CometBFT BFT consensus — battle-tested in production
- Requires 2/3+ validator stake for consensus
- 1-block finality — no reorganizations possible

### Codebase Security
- Fork of Evmos v16.0.4 — extensively audited upstream codebase
- Cosmos SDK v0.47 — audited by multiple firms
- Regular security reviews

### Validator Slashing
- **Double-sign:** 5% slash + permanent jailing
- **Downtime:** 1% slash + temporary jailing
- **Signed blocks window:** 100 blocks

### Bug Bounty
Security researchers are encouraged to report vulnerabilities to: contact@yonetwork.org

---

## 10. Team & Governance

### Governance

All protocol changes are decided by on-chain governance:
- **Proposal deposit:** 10,000,000 aevmos
- **Voting period:** 48 hours
- **Quorum:** 33.4% of staked YO
- **Pass threshold:** 50% Yes
- **Veto threshold:** 33.4% NoWithVeto

### Current Validators

| Validator | Stake | Commission |
|-----------|-------|------------|
| yo-node-1 | 1,000,000 YO | 5% |
| YoClub | 9,990,000 YO | 5% |

---

## 11. Roadmap

### Phase 1 — Foundation ✅ Complete
- [x] Mainnet launch
- [x] Block explorer live
- [x] RPC and API endpoints
- [x] MetaMask integration
- [x] Validator set established
- [x] BSC token deployment

### Phase 2 — Ecosystem (In Progress)
- [ ] DEX integration
- [ ] Bridge to BSC (USDT ↔ YO)
- [ ] IBC connections to major Cosmos chains
- [ ] Mobile wallet app
- [ ] NFT marketplace

### Phase 3 — Growth
- [ ] Exchange listings (CEX)
- [ ] DeFi protocols
- [ ] Developer grants program
- [ ] Cross-chain DApps
- [ ] Governance decentralization

### Phase 4 — Scale
- [ ] Layer 2 solutions
- [ ] Enterprise partnerships
- [ ] Global expansion

---

## 12. Legal & Compliance

### Disclaimer
YO is a utility token that provides access to network resources (gas fees), staking rewards, and governance rights on the Yo Network blockchain. It is not intended to be a security, investment contract, or financial instrument.

### Jurisdiction
Yo Network is a decentralized protocol. Token holders should consult legal counsel regarding regulations in their jurisdiction.

### KYC/AML
Yo Network is a public, permissionless blockchain. Individual exchanges and service providers are responsible for their own KYC/AML compliance.

---

## 13. Exchange Integration Guide

### For Centralized Exchanges (CEX)

#### Node Setup
1. Run a Yo Network full node (see [INSTALL.md](INSTALL.md))
2. Enable the JSON-RPC API (port 8545)
3. Enable the REST API (port 1317)

#### Deposit Integration

```javascript
// Connect to Yo Network via JSON-RPC
const Web3 = require('web3');
const web3 = new Web3('https://rpc.yonetwork.org');

// Generate deposit address (standard Ethereum key derivation)
const account = web3.eth.accounts.create();
console.log('Deposit address:', account.address);

// Monitor deposits
web3.eth.subscribe('newBlockHeaders', async (block) => {
  const blockData = await web3.eth.getBlock(block.hash, true);
  // Check transactions for deposits to your addresses
});
```

#### Withdrawal Integration

```javascript
// Sign and send withdrawal transaction
const tx = {
  from: exchangeHotWallet,
  to: userAddress,
  value: web3.utils.toWei(amount, 'ether'), // YO uses 18 decimals
  gas: 21000,
  gasPrice: web3.utils.toWei('1', 'gwei')
};

const signed = await web3.eth.accounts.signTransaction(tx, privateKey);
const receipt = await web3.eth.sendSignedTransaction(signed.rawTransaction);
```

#### Transaction Confirmation
- **Recommended confirmations:** 10 blocks (~30 seconds)
- **Block time:** ~3 seconds
- **Finality:** Instant (BFT consensus)

#### API Endpoints for Exchange Integration

```
# Get latest block
GET https://explorer.yonetwork.org/api/v2/blocks?type=block

# Get address transactions
GET https://explorer.yonetwork.org/api/v2/addresses/{address}/transactions

# Get transaction by hash
GET https://explorer.yonetwork.org/api/v2/transactions/{hash}

# Get address balance
GET https://explorer.yonetwork.org/api/v2/addresses/{address}
```

#### Denomination Notes
```
1 YO = 1,000,000,000,000,000,000 aevmos (10^18)
Display: "YO" with 18 decimal places
JSON-RPC: values in wei (same as ETH, 10^18)
```

### For Decentralized Exchanges (DEX)

Yo Network is fully EVM compatible. Any Uniswap V2/V3 fork can be deployed directly on Yo Network.

---

## 14. Contact Information

| Type | Details |
|------|---------|
| **General Inquiries** | contact@yonetwork.org |
| **Exchange Listings** | contact@yonetwork.org |
| **Technical Support** | contact@yonetwork.org |
| **Security Reports** | contact@yonetwork.org |
| **Telegram** | https://t.me/yonetwork25|
| **GitHub** | https://github.com/yonetwork25/YoNetwork |
| **Website** | https://yonetwork.org |
| **Explorer** | https://explorer.yonetwork.org |

---

*This document was prepared by the Yo Network team for exchange listing purposes. All technical specifications are accurate as of the date of publication.*

*Last updated: April 2026*
