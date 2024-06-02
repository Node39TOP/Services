### Hardware minumum: 

### 2 Cores
### 2GB RAM
### 40 GB SSD
### Ubuntu 22 - x86 or arm64
```
Website: http://hedgeblock.io
Telegram: https://t.me/hedgeblockio
Discord: https://discord.com/invite/fxmUNYTayQ
X: https://twitter.com/hedgeblockio
```
# Node39 support:
```
RPC: https://hedge-testnet-rpc.node39.top
API: https://hedge-testnet-api.node39.top
Snapshort: Every 12 hours
Dashboard: https://dashboard.node39.top/Hedge-Testnet 
```

### Node39 support:
# RPC: https://hedge-testnet-rpc.node39.top
# API: https://hedge-testnet-api.node39.top
# Snapshort: Every 12 hours
# Explorer: https://explorer.node39.top/Hedge-Testnet 

### Install Dependencies:
```
sudo apt update && sudo apt upgarade -y
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```

### Install GO: (amd64 - x86)
```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.21.8.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

### Install GO: (arm64)
```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.21.8.linux-arm64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```

### Dowload binary & ibwasmvm: x86
```
// Ibwasmvm
set -eux; \
https://github.com/CosmWasm/wasmvm/releases/download/v2.0.1/libwasmvm.x86_64.so

// Binary
mkdir -p $HOME/go/bin
sudo wget -O hedged https://github.com/hedgeblock/testnets/releases/download/v0.1.0/hedged_linux_amd64_v0.1.0
chmod +x hedged
sudo mv hedged /go/bin
```

### Dowload ibwasmvm: arm64
```
// Ibwasmvm
set -eux; \
https://github.com/CosmWasm/wasmvm/releases/download/v2.0.1/libwasmvm.aarch64.so

// Binary
mkdir -p $HOME/go/bin
sudo wget -O hedged https://github.com/hedgeblock/testnets/releases/download/v0.1.0/hedged_linux_amd64_v0.1.0
chmod +x hedged
sudo mv hedged /go/bin
```

### Set chain and Name Hedge : Change <Change-Name> 
```
hedged init <Change-Name> --chain-id=berberis-1
```

### Download Genesis & addressbook:
```
curl -Ls https://node39.top/testnet/hedge/genesis.json > $HOME/.junction/config/genesis.json 
curl -Ls https://node39.top/testnet/hedge/addrbook.json > $HOME/.junction/config/addrbook.json 
```

### Create Service:
```
sudo tee /etc/systemd/system/hedged.service > /dev/null <<EOF
[Unit]
Description=hedged Daemon
After=network-online.target
[Service]
User=root
ExecStart=$(which hedged) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
 
### Peer:
```
peers=$(curl -s https://node39.top/testnet/hedge/peers.txt)
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" ~/.hedge/config/config.toml

sudo systemctl daemon-reload
sudo systemctl restart hedged
sudo journalctl -u hedged -f --no-hostname -o cat
```

### Snapshort: 
```
cp $HOME/.hedge/data/priv_validator_state.json $HOME/.warden/priv_validator_state.json.backup
wardend tendermint unsafe-reset-all --home $HOME/.warden
rm -rf $HOME/.hedge/data $HOME/.hedge/wasmPath
curl https://node39.top/testnet//hedge.tar.lz4  | lz4 -dc - | tar -xf - -C $HOME/.hedge
mv $HOME/.hedge/priv_validator_state.json.backup $HOME/.hedge/data/priv_validator_state.json
```

### Wallet:
```
// Add new Wallet | Save seed
hedged keys add wallet

// Recover existing key
hedged keys add wallet --recover

//List All Keys
hedged keys list
```

### Wallet Balance:
```
hedged q bank balances $(hedged keys show wallet -a)
```

### Check sync status: False -> Synchronization completed.
```
hedged status 2>&1 | jq .SyncInfo.catching_up
```

### VALIDATOR
### Create Validator: 
Note: 
Change xxxxxxxxxx
The number of uhegde can be changed
1000000 uhedge = 1 hedge
Backup: cat $HOME/.hedge/config/priv_validator_key.json

```
hedged tx staking create-validator \
--amount 1000000uhedge \
--pubkey $(hedged tendermint show-validator) \
--chain-id berberis-1 \
--min-self-delegation 1 \
--commission-max-change-rate 0.01 \
--commission-max-rate 0.2 \
--commission-rate 0.05 \
--moniker "XXXXXX" \
--identity "XXXXXX" \
--details "XXXXXX" \
--website "XXXXXX" \
--security-contact "XXXXXX" \
--gas-prices="0.025uhedge" \
--gas="auto" \
--gas-adjustment="1.5" \
--from "wallet"
```

### Edit Existing Validator
```
hedged tx staking edit-validator \
--chain-id berberis-1 \
--new-moniker "XXXXXX" \
--identity "XXXXXX" \
--details "XXXXXX" \
--website "XXXXXX" \
--security-contact "XXXXXX" \
--gas-prices="0.025uhedge" \
--gas="auto" \
--gas-adjustment="1.5" \
--from wallet
```

### Delegate your Validator
Note: The number of uhegde can be changed
```
hedged tx staking delegate $(hedged keys show wallet --bech val -a) 1000000uhedge --from wallet --chain-id berberis-1 --gas-prices=0.005uhedge  --gas-adjustment 1.5 --gas auto -y
```

### Unjail validator
```
hedged tx slashing unjail --from wallet --chain-id berberis-1 --gas-prices=0.005uhedge  --gas-adjustment 1.5 --gas auto -y
```

### Command
```
// Reload Service
sudo systemctl daemon-reload

// Enable Service
sudo systemctl enable hedged

// Disable Service
sudo systemctl disable hedged

// Start Service
sudo systemctl start hedged

// Stop Service
sudo systemctl stop hedged

// Restart Service
sudo systemctl restart hedged

// Check Service Status
sudo systemctl status hedged

// Check Service Logs
sudo journalctl -u hedged -f --no-hostname -o cat
```














