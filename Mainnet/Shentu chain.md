# Shentu chain Version 2.2
### Install Dependencies:
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

### Install GO: (arm64)
```sh
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

### Install Shentu chain:
```sh
cd $HOME
rm -rf shentu
git clone https://github.com/shentufoundation/shentu
cd shentu
git checkout v2.9.0
make install
shentud version
```

### Set chain and Name Shentu chain:
```sh
shentud init <Change-Name> --chain-id=shentu-2.2
```

### Download Genesis & addressbook:
```sh
curl -Ls https://node39.top/Mainnet/Shentu/genesis.json > $HOME/.shentu/config/genesis.json 
curl -Ls https://node39.top/Mainnet/Shentu/addrbook.json > $HOME/.shentu/config/addrbook.json
```

### Create Service:
```sh
sudo tee /etc/systemd/system/shentud.service > /dev/null <<EOF
[Unit]
Description=shentud
After=network-online.target
[Service]
User=$USER
ExecStart=$(which shentud) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable shentud
```

### State sync:
```sh
sed -i "/\[statesync\]/, /^enable =/ s/=.*/= false/;\
/^rpc_servers =/ s|=.*|= \"\"|;\
/^trust_height =/ s/=.*/= 0/;\
/^trust_hash =/ s/=.*/= \"\"/" $HOME/.shentud/config/config.toml						 

sudo systemctl stop shentud 
SNAP_RPC="https://rpc.shentu-2.2.shentu.aviaone.com:443"
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 1000)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

sed -i "/\[statesync\]/, /^enable =/ s/=.*/= true/;\
/^rpc_servers =/ s|=.*|= \"$SNAP_RPC,$SNAP_RPC\"|;\
/^trust_height =/ s/=.*/= $BLOCK_HEIGHT/;\
/^trust_hash =/ s/=.*/= \"$TRUST_HASH\"/" $HOME/.shentud/config/config.toml

sudo systemctl stop shentud && shentud tendermint unsafe-reset-all --home $HOME/.shentud --keep-addr-book
```






