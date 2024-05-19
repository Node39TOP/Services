- Recommended Hardware: 4 Core, 8Gb ram, 200Gb SSD NVMe
- Minumum Hardware: 2 Core, 4Gb ram, 40Gb SSD NVMe
- Ubuntu 22.04, x86

Install Dependencies:
```sh
sudo apt update && sudo apt upgarade -y
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```
### Install GO: (amd64 - x86)
```sh
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

### Insstall Airchains:
```sh
cd $HOME
wget https://github.com/airchains-network/junction/releases/download/v0.1.0/junctiond
chmod +x junctiond
sudo mv junctiond /usr/local/bin/
cd $HOME
junctiond version
```

### Set chain and Name Galactica: *Change <Change-Name>*
```sh
junctiond config chain-id junction
junctiond init <Change-Name> --chain-id junction
```

### Download Genesis & addressbook:
```sh
curl -Ls https://node39.top/testnet/airchains/genesis.json > $HOME/.junction/config/genesis.json 
curl -Ls https://node39.top/testnet/airchains/addrbook.json > $HOME/.junction/config/addrbook.json
```
### Create Service:
```sh
sudo tee /etc/systemd/system/junctiond.service > /dev/null <<EOF
[Unit]
Description=junctiond
After=network-online.target
[Service]
User=root
ExecStart=$(which junctiond) start
Restart=always
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable junctiond
```

### Download Snapshort: (Every 4 hours)
```sh
junctiond tendermint unsafe-reset-all --home ~/.junction/ --keep-addr-book
wget https://node39.top/testnet/airchains/snapshort-airchains.tar.lz4
lz4 -c -d snapshort-airchains.tar.lz4 | tar -x -C $HOME/.junction
rm -rf snapshort-airchains.tar.lz4
```

### Wallet:
```sh
// Create wallet
junctiond keys add wallet

// Recover wallet
junctiond keys add wallet --recover

// Check Balance
junctiond q bank balances $(junctiond keys show wallet -a)
```

### Check sync: (False -> done)
```sh
junctiond status 2>&1 | jq .sync_info
```

### Create validator:
```sh
cd $HOME

// Check validator public key
junctiond comet show-validator

Ex: {"@type":"/cosmos.crypto.ed25519.PubKey","key":"sElDJvnNcALvCAirlerzK9tzsf5CXPmDPVz0Bvm9EjQ="}

// Create & edit file json
nano validator.json

{
	"pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"sElDJvnNcALvCAirlerzK9tzsf5CXPmDPVz0Bvm9EjQ="},
	"amount": "1000000amf",
	"moniker": "<change-name>",
	"identity": "",
	"website": "",
	"security": "",
	"details": "",
	"commission-rate": "0.1",
	"commission-max-rate": "0.2",
	"commission-max-change-rate": "0.01",
	"min-self-delegation": "1"
}

// Create validator
junctiond tx staking create-validator validator.json --from wallet --chain-id junction --fees 5000amf -y
```

### Delegate Token to your own validator:
```sh
junctiond tx staking delegate $(junctiond keys show wallet --bech val -a)  1000000amf --from wallet --chain-id junction --fees 5000amf -y
```
### Withdraw rewards and commission from your validator:
```sh
junctiond tx distribution withdraw-rewards $(junctiond keys show wallet --bech val -a) --from wallet --chain-id junction --fees 5000amf -y 
```
### Unjail validator:
```sh
junctiond tx slashing unjail --from wallet --chain-id junction --fees 5000amf -y
```

### Command:
```sh
// Reload Service
sudo systemctl daemon-reload

// Enable Service
sudo systemctl enable junctiond

// Disable Service
sudo systemctl disable junctiond

// Start Service
sudo systemctl start junctiond

// Stop Service
sudo systemctl stop junctiond

// Restart Service
sudo systemctl restart junctiond

// Check Service Status
sudo systemctl status junctiond

// Check Service Logs
sudo journalctl -u junctiond -f --no-hostname -o cat
```

``` Detele node:
```sh
sudo systemctl stop junctiond
sudo systemctl disable junctiond
sudo rm -rf /etc/systemd/system/junctiond.service
sudo rm $(which junctiond)
sudo rm -rf $HOME/.junction
sed -i "/AIRCHAIN_/d" $HOME/.bash_profile
```

### More infomation:
```sh
Website: https://www.airchains.io/
Twitter: https://twitter.com/airchains_io
Discord: https://discord.gg/airchains
Github: https://github.com/airchains-network
Faucet: Discord
```
