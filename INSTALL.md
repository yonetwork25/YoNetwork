# Yo Network — Installation Guide

This guide covers everything from running a full node to becoming a validator on Yo Network.

---

## Table of Contents

1. [System Requirements](#system-requirements)
2. [Quick Install](#quick-install)
3. [Full Node Setup](#full-node-setup)
4. [Validator Setup](#validator-setup)
5. [Configuration Reference](#configuration-reference)
6. [Useful Commands](#useful-commands)
7. [Troubleshooting](#troubleshooting)

---

## System Requirements

### Minimum (Full Node)

| Component | Requirement |
|-----------|-------------|
| CPU | 4 cores |
| RAM | 8 GB |
| Storage | 200 GB SSD |
| OS | Ubuntu 20.04 / 22.04 LTS |
| Network | 100 Mbps |

### Recommended (Validator)

| Component | Requirement |
|-----------|-------------|
| CPU | 8+ cores |
| RAM | 16 GB |
| Storage | 500 GB NVMe SSD |
| OS | Ubuntu 22.04 LTS |
| Network | 1 Gbps |

---

## Quick Install

```bash
# Install dependencies
sudo apt update && sudo apt install -y git curl wget build-essential jq

# Install Go 1.21.6
wget https://go.dev/dl/go1.21.6.linux-amd64.tar.gz
sudo tar -C /usr/local -xzf go1.21.6.linux-amd64.tar.gz
echo 'export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin' >> ~/.bashrc
source ~/.bashrc
go version

# Build Yo Network
git clone https://github.com/yonetwork25/YoNetwork.git
cd YoNetwork
make install
evmosd version
```

---

## Full Node Setup

### Step 1 — Initialize the Node

```bash
evmosd init <YOUR_MONIKER> --chain-id evmos_9000-1
```

Replace `<YOUR_MONIKER>` with a name for your node (e.g. `my-yo-node`).

### Step 2 — Download Genesis

```bash
# Download the Yo Network genesis file
curl -o ~/.evmosd/config/genesis.json \
  https://raw.githubusercontent.com/yonetwork25/YoNetwork/main/genesis.json
```

Or copy from a running node:
```bash
scp root@195.201.172.110:~/.evmosd/config/genesis.json ~/.evmosd/config/genesis.json
```

### Step 3 — Configure Peers

```bash
# Add seed/persistent peers
sed -i 's/persistent_peers = ""/persistent_peers = "44b4e3e5c7fe30b16a0f9750de057c9dfb046013@195.201.172.110:26656"/' \
  ~/.evmosd/config/config.toml
```

### Step 4 — Configure app.toml

```bash
# Set minimum gas prices
sed -i 's/minimum-gas-prices = "0aevmos"/minimum-gas-prices = "0.0025aevmos"/' \
  ~/.evmosd/config/app.toml

# Enable JSON-RPC
sed -i '/\[json-rpc\]/,/\[/ s/enable = false/enable = true/' ~/.evmosd/config/app.toml

# Enable CORS
sed -i 's/enabled-unsafe-cors = false/enabled-unsafe-cors = true/' ~/.evmosd/config/app.toml
```

### Step 5 — Set Chain ID in client.toml

```bash
sed -i 's/chain-id = ""/chain-id = "evmos_9000-1"/' ~/.evmosd/config/client.toml
```

### Step 6 — Create Systemd Service

```bash
sudo tee /etc/systemd/system/yonetwork.service > /dev/null << EOF
[Unit]
Description=Yo Network Node
After=network.target

[Service]
Type=simple
User=$USER
ExecStart=$(which evmosd) start --keyring-backend test --home $HOME/.evmosd
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable yonetwork
sudo systemctl start yonetwork
```

### Step 7 — Check Sync Status

```bash
# Check if node is syncing
evmosd status | python3 -c "
import json,sys
s = json.load(sys.stdin)
print('Block:', s['SyncInfo']['latest_block_height'])
print('Catching up:', s['SyncInfo']['catching_up'])
"
```

Wait for `catching_up: false` before proceeding.

---

## Validator Setup

### Prerequisites
- Full node running and fully synced
- At least 1,000,000 YO tokens for self-delegation

### Step 1 — Create Validator Key

```bash
evmosd keys add <VALIDATOR_KEY_NAME> --keyring-backend test
```

> ⚠️ **Save your mnemonic phrase securely. It cannot be recovered if lost.**

### Step 2 — Fund Your Validator Address

Send YO tokens to your validator address. You can purchase YO from exchanges or get them from the team.

```bash
# Check your address
evmosd keys show <VALIDATOR_KEY_NAME> --keyring-backend test -a

# Check balance
evmosd query bank balances <YOUR_ADDRESS>
```

### Step 3 — Create the Validator

```bash
evmosd tx staking create-validator \
  --amount=<STAKE_AMOUNT>aevmos \
  --from=<VALIDATOR_KEY_NAME> \
  --pubkey=$(evmosd tendermint show-validator) \
  --moniker="<YOUR_MONIKER>" \
  --chain-id=evmos_9000-1 \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --keyring-backend=test \
  --gas=300000 \
  --gas-prices=1000000000aevmos \
  --details="<DESCRIPTION>" \
  --website="<YOUR_WEBSITE>" \
  -y
```

**Example** (1M YO stake):
```bash
evmosd tx staking create-validator \
  --amount=1000000000000000000000000aevmos \
  --from=myvalidator \
  --pubkey=$(evmosd tendermint show-validator) \
  --moniker="MyValidator" \
  --chain-id=evmos_9000-1 \
  --commission-rate="0.05" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.01" \
  --min-self-delegation="1" \
  --keyring-backend=test \
  --gas=300000 \
  --gas-prices=1000000000aevmos \
  -y
```

> Note: 1 YO = 1,000,000,000,000,000,000 aevmos (10^18)

### Step 4 — Verify Validator

```bash
evmosd query staking validators | grep -A 10 "YOUR_MONIKER"
```

### Step 5 — Edit Validator (Optional)

```bash
evmosd tx staking edit-validator \
  --moniker="New Name" \
  --details="Updated description" \
  --website="https://yourwebsite.com" \
  --from=<VALIDATOR_KEY_NAME> \
  --chain-id=evmos_9000-1 \
  --keyring-backend=test \
  --gas=auto \
  --gas-prices=1000000000aevmos \
  -y
```

### Unjail Validator

If your validator gets jailed (missed too many blocks):

```bash
evmosd tx slashing unjail \
  --from=<VALIDATOR_KEY_NAME> \
  --chain-id=evmos_9000-1 \
  --keyring-backend=test \
  --gas=auto \
  --gas-prices=1000000000aevmos \
  -y
```

---

## Configuration Reference

### config.toml Key Settings

```toml
# P2P
[p2p]
laddr = "tcp://0.0.0.0:26656"
persistent_peers = "44b4e3e5c7fe30b16a0f9750de057c9dfb046013@195.201.172.110:26656"

# RPC
[rpc]
laddr = "tcp://0.0.0.0:26657"
cors_allowed_origins = ["*"]

# Consensus
[consensus]
timeout_commit = "3s"
```

### app.toml Key Settings

```toml
minimum-gas-prices = "0.0025aevmos"
pruning = "default"

[api]
enable = true
address = "tcp://0.0.0.0:1317"
enabled-unsafe-cors = true

[json-rpc]
enable = true
address = "0.0.0.0:8545"
ws-address = "0.0.0.0:8546"
enable-unsafe-cors = true
```

---

## Useful Commands

### Node Management

```bash
# Start node
systemctl start yonetwork

# Stop node
systemctl stop yonetwork

# Check status
systemctl status yonetwork

# View logs
journalctl -u yonetwork -f

# Check sync
evmosd status | jq .SyncInfo
```

### Key Management

```bash
# List keys
evmosd keys list --keyring-backend test

# Show address
evmosd keys show <KEY_NAME> --keyring-backend test -a

# Show EVM address
evmosd keys show <KEY_NAME> --keyring-backend test --bech eth

# Recover from mnemonic
evmosd keys add <KEY_NAME> --recover --keyring-backend test
```

### Token Operations

```bash
# Check balance
evmosd query bank balances <ADDRESS>

# Send tokens
evmosd tx bank send <FROM_KEY> <TO_ADDRESS> <AMOUNT>aevmos \
  --chain-id evmos_9000-1 \
  --keyring-backend test \
  --gas auto \
  --gas-prices 1000000000aevmos \
  -y

# Delegate to validator
evmosd tx staking delegate <VALIDATOR_ADDR> <AMOUNT>aevmos \
  --from <KEY> \
  --chain-id evmos_9000-1 \
  --keyring-backend test \
  --gas auto \
  --gas-prices 1000000000aevmos \
  -y

# Claim staking rewards
evmosd tx distribution withdraw-all-rewards \
  --from <KEY> \
  --chain-id evmos_9000-1 \
  --keyring-backend test \
  --gas auto \
  --gas-prices 1000000000aevmos \
  -y
```

### Governance

```bash
# List proposals
evmosd query gov proposals

# Vote yes
evmosd tx gov vote <PROPOSAL_ID> yes \
  --from <KEY> \
  --chain-id evmos_9000-1 \
  --keyring-backend test \
  --gas auto \
  --gas-prices 1000000000aevmos \
  -y
```

### Staking Queries

```bash
# List all validators
evmosd query staking validators

# Show validator details
evmosd query staking validator <VALIDATOR_ADDR>

# Show delegations
evmosd query staking delegations <DELEGATOR_ADDR>

# Show staking rewards
evmosd query distribution rewards <DELEGATOR_ADDR>
```

---

## Troubleshooting

### Node won't start

```bash
# Check logs
journalctl -u yonetwork -n 50 --no-pager

# Reset node (WARNING: deletes all chain data)
evmosd tendermint unsafe-reset-all --home ~/.evmosd
```

### Wrong chain-id error

```bash
sed -i 's/chain-id = ""/chain-id = "evmos_9000-1"/' ~/.evmosd/config/client.toml
```

### Validator jailed

Check if you've been signing blocks:
```bash
evmosd query slashing signing-info $(evmosd tendermint show-validator)
```

### Port already in use

```bash
# Check what's using port 26657
lsof -i :26657

# Change RPC port in config.toml
sed -i 's/laddr = "tcp:\/\/0.0.0.0:26657"/laddr = "tcp:\/\/0.0.0.0:26660"/' \
  ~/.evmosd/config/config.toml
```

---

## Support

- **Telegram:** https://t.me/certitude_cars
- **Email:** contact@yonetwork.org
- **Explorer:** https://explorer.yonetwork.org
