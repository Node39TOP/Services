### System: 
`4 core, 8gb ram, 80 disk`
`Ubuntu 22 amd64 -x86`

### Install Dependencies
```sh
sudo apt update && apt upgrade -y
sudo apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev libgmp3-dev tar clang bsdmainutils ncdu unzip llvm libudev-dev make protobuf-compiler -y
```

### Download binary & json:
```sh
cd $HOME
sudo mkdir -p $HOME/.tangle && cd $HOME/.tangle
sudo wget -O tangle https://github.com/webb-tools/tangle/releases/download/v1.0.3/tangle-txpool-linux-amd64
sudo chmod 744 tangle
sudo mv tangle /usr/bin/
sudo tangle --version

sudo wget -O $HOME/.tangle/tangle-standalone.json "https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/mainnet/tangle-mainnet.json"
sudo chmod 744 ~/.tangle/tangle-standalone.json
```

### Create Service: (Change moniker)
```sh
sudo tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/bin/tangle \
  --base-path $HOME/.tangle/data/ \
  --name moniker \                                #Change Moniker here
  --chain $HOME/.tangle/tangle-mainnet.json \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 30333 \
  --validator \
  --no-mdns \
  --telemetry-url "wss://telemetry.polkadot.io/submit/ 1"
[Install]
WantedBy=multi-user.target
EOF
```
### Create keys: (change your seeds)
```sh
KEY="your seeds"

tangle key insert --base-path $HOME/.tangle/data/ \
--chain tangle-mainnet \
--scheme Sr25519 \
--suri "$KEY" \
--key-type acco



tangle key insert --base-path $HOME/.tangle/data/ \
--chain tangle-mainnet \
--scheme Sr25519 \
--suri "$KEY" \
--key-type babe

tangle key insert --base-path $HOME/.tangle/data/ \
--chain tangle-mainnet \
--scheme Sr25519 \
--suri "$KEY" \
--key-type imon

tangle key insert --base-path $HOME/.tangle/data/ \
--chain tangle-mainnet \
--scheme Ecdsa \
--suri "$KEY" \
--key-type role


tangle key insert --base-path $HOME/.tangle/data/ \
--chain tangle-mainnet \
--scheme Ed25519 \
--suri "$KEY" \
--key-type gran
```

### Command:
```sh
sudo systemctl daemon-reload
sudo systemctl enable tangle
sudo systemctl restart tangle && sudo journalctl -u tangle -f -o cat
```
