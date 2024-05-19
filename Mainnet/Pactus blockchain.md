## System Requirements: 
Ubuntu 22.04
Open port: 21888 TCP and UDP
Min: 1 CPU; 2Gb Ram; 20Gb SSD

## Server preparation:
```sh
sudo apt update && sudo apt upgrade -y && sudo apt install tmux git curl -y && sudo apt install make clang pkg-config libssl-dev build-essential -y
```

## Download Pactus v1.1.4: (amd64 - x86)
```sh
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.4/pactus-cli_1.1.4_linux_amd64.tar.gz && tar -xzf pactus-cli_1.1.4_linux_amd64.tar.gz && rm -rf pactus-cli_1.1.4_linux_amd64.tar.gz && mv pactus-cli_1.1.4 node_pactus && cd node_pactus
```

## Download Pactus v1.1.4: (arm64 - M1)
```sh
cd $HOME && rm -rf node_pactus && wget https://github.com/pactus-project/pactus/releases/download/v1.1.4/pactus-cli_1.1.4_linux_arm64.tar.gz && tar -xzf pactus-cli_1.1.4_linux_arm64.tar.gz && rm -rf pactus-cli_1.1.4_linux_arm64.tar.gz && mv pactus-cli_1.1.4 node_pactus && cd node_pactus
```

## Setup:
```sh
sudo ./pactus-daemon init
```
*Note: It is recommended to run 1 validator.
Save all important information in this step `(Address validator, Wallet & seed)`.

`If you already had a wallet before, you can use this command to restore the wallet`

```sh
./pactus-daemon init --restore "<your-seed>"
```

## Start Pactus: Run in tmux
```sh
tmux new -s pactus && sudo ./pactus-daemon start
```

# Wallet seed:
```sh
./pactus-wallet seed
```

## List of Addresses:
```sh
./pactus-wallet address all
```

## Check Balance:
```sh
./pactus-wallet address balance <ADDRESS>
```

## Bond to the validator
```sh
./pactus-wallet tx bond <Reward address> <Validator address> <AMOUNT>
```

## Send token
```sh
./pactus-wallet tx transfer <reward address> <receiver address> <AMOUNT>
```














