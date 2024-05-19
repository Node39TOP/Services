`System minimum: 2core - 4gb ram - 40gb disk`
`Ubuntu 22 or higher`
`amd64-x86 or arm64-m1`

### Download binaries: 
```sh
// amd64 - x86
wget https://github.com/massalabs/massa/releases/download/MAIN.2.1/massa_MAIN.2.1_release_linux.tar.gz && tar zxvf massa_MAIN.2.1_release_linux.tar.gz

// arm64 - m1
wget https://github.com/massalabs/massa/releases/download/MAIN.2.1/massa_MAIN.2.1_release_linux_arm64.tar.gz && tar zxvf massa_MAIN.2.1_release_linux_arm64.tar.gz
```

### Config + Service:
```sh
// Change IP
sudo tee <<EOF >/dev/null $HOME/massa/massa-node/config/config.toml 
[protocol] 
routable_ip = "IP_ADDRESS"
EOF

//Change Pass
printf "[Unit]
Description=Massa Node
After=network-online.target
[Service]
User=$USER
WorkingDirectory=$HOME/massa/massa-node
ExecStart=$HOME/massa/massa-node/massa-node -p YOUR_PASSWORD
Restart=on-failure
RestartSec=3
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target" > /etc/systemd/system/massad.service

//Restart + check log
sudo systemctl daemon-reload
sudo systemctl enable massad
sudo systemctl restart massad
sudo journalctl -f -n 100 -u massad
```

### Wallet
```sh
// Create wallet
cd $HOME/massa/massa-client/
./massa-client
Command create wallet: wallet_generate_secret_key

// Restore wallet
wallet_add_secret_keys Secretkey

// Wallet add it to stake
node_start_staking WALLET_ADDRESS
```

### Check wallet info again: (Change pass)
```sh
cd /$HOME/massa/massa-client/ && ./massa-client -p PASS wallet_info
```

### Buy or sell rolls: (Change info your node)
```sh
cd /$HOME/massa/massa-client/ && ./massa-client -p YOUR_PASSWORD
buy_rolls YOUR_WALLET_ADDRESS Number_of_rolls Commission

# In the above case, the command looks like this:

buy_rolls A190qwertyuiopasdfghjkl1234567890 1 0
```

### Command:
```sh
sudo systemctl restart massad
sudo systemctl stop massad
sudo journalctl -f -n 100 -u massad
cd /root/massa/massa-client/; ./massa-client; cd
```

### sudo systemctl restart massad
```sh
sudo systemctl stop massad
sudo journalctl -f -n 100 -u massad
cd /root/massa/massa-client/; ./massa-client; cd
```

### More infomation:

// Website
https://massa.net/

// Explorer
https://explorer.massa.net/

// Docs
https://docs.massa.net/

// Discord
https://discord.gg/massa

// Telegram
https://t.me/massanetwork

// Github
https://github.com/massalabs

// X
https://twitter.com/massalabs


