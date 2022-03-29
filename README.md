# Send offline transactions for TON blockchain

## Why?

Your wallet private key / 24 word mnemonic is the main secret you must protect in order to secure your funds from theft. The most secure way to protect secrets is via cold storage - meaning hold the secret on a "cold" device that isn't connected to the Internet. By completely disconnecting the secret from the outside world, no hacker could gain access to it.

This is often achieved through the use of hardware wallets (like Trezor and Ledger) that hold the secret and prevent it from being accessed. Unfortunately, the popular hardware wallets don't support TON yet officially. The protocol defined in this repo is similar in the sense that a special offline computer assumes the role of the hardware wallet.

## The workflow

#### Setup offline wallet

1. **Setup offline computer**<br>This special computer will hold your TON private key and should never be connected to the Internet
2. **Sign deployment offline**<br>Use the offline computer to sign a new transaction for deploying the wallet contract (create BOC file)
3. **Move BOC and send**<br>Move the BOC file to a different online computer and transmit it to TON mainnet using a lite client

#### Send TON coins

4. **Sign transfer offline**<br>Use the offline computer to sign a new transaction for sending TON coins (create BOC file)
5. **Move BOC and send**<br>Move the BOC file to a different online computer and transmit it to TON mainnet using a lite client

## Step 1: Setup offline computer

This computer should never be connected to the Internet, so all tools must be installed on it in advance:

### 1.1. Build `fift` executable

  This command line tool is part of the TON toolchain. Its source code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Build it from source code by following the [official instructions](https://ton.org/docs/#/compile?id=fift) or find a trusted source for [pre-built binaries](https://github.com/ton-defi-org/ton-binaries) that you can just download and use.
  
  Example:
  ```
  sudo apt update
  sudo apt install git make cmake g++ libssl-dev zlib1g-dev wget
  cd ~ && git clone https://github.com/newton-blockchain/ton.git
  cd ~/ton && git submodule update --init
  mkdir ~/ton/build && cd ~/ton/build && cmake .. -DCMAKE_BUILD_TYPE=Release && make -j 4
  mkdir ~/ton-offline && cp ~/ton/build/crypto/fift ~/ton-offline
  ```
  
### 1.2. Download `fift-lib` library
  
  This dependency is a standard collection of about 8 fift files. They are part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download the files from https://github.com/newton-blockchain/ton/tree/master/crypto/fift/lib.
  
  Assuming you downloaded the files to `~/downloads/fift-lib`, set the `fift-lib` directory as the environment variable `FIFTPATH` by running `export FIFTPATH=~/downloads/fift-lib`.
  
  Example:
  ```
  cp -r ~/ton/crypto/fift/lib ~/ton-offline/fift-lib
  export FIFTPATH=~/ton-offline/fift-lib
  ```
  
### 1.3. Download `wallet-v3.fif`, `new-wallet-v3.fif` and `wallet-v3-code.fif`

  The TON core team publishes the official template for a wallet smart contract. In the time of writing, the most recent version is wallet V3. The code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download [wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3.fif), [new-wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/new-wallet-v3.fif) and [wallet-v3-code.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3-code.fif) from https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont.
  
  Example:
  ```
  cp ~/ton/crypro/smartcont/wallet-v3.fif ~/ton-offline
  cp ~/ton/crypro/smartcont/new-wallet-v3.fif ~/ton-offline
  cp ~/ton/crypro/smartcont/wallet-v3-code.fif ~/ton-offline
  ```

### 1.4. Build `tonweb-mnemonic.js` JS library

  This JavaScript library helps manipulate 24 word mnemonics (BIP39) and convert them to functional private keys that can sign TON transactions. The library source code is available [here](https://github.com/toncenter/tonweb-mnemonic). Build it from source code by running `npm install && npm run build:web && cp dist/web/index.js tonweb-mnemonic.js` or find a trusted source for a [pre-built version](tonweb-mnemonic.js) that you can just download and use. 

  Example:
  ```
  cd ~ && git clone https://github.com/toncenter/tonweb-mnemonic.git
  cd ~/tonweb-mnemonic && npm install && npm run build:web
  cp ~/tonweb-mnemonic/dist/web/index.js ~/ton-offline/tonweb-mnemonic.js
  ```

### 1.5. Download `ton-mnemonic-pk.html`

  This offline HTML relies on `tonweb-mnemonic.js` and provides an easy-to-use interface where you can convert an existing 24 word mnemonic (BIP39) to a 32-byte TON-compatible private key file (`mywallet.pk`). The private key file (`mywallet.pk`) is needed as argument to `fift` executable for signing transactions. The HTML is part of this repo and can be downloaded [here](ton-mnemonic-pk.html).
  
  Example:
  ```
  wget https://raw.github.com/ton-defi-org/ton-offline-transaction/master/ton-mnemonic-pk.html -P ~/ton-offline
  ```

## Step 2: Sign deployment offline

### 2.1. Ready `mywallet.pk`

  The primary secret stored on the offline computer is the private key file. If you only have a 24 word mnemonic (BIP39), open `ton-mnemonic-pk.html` using a web browser (offline), type the mnemonic into the page and use it to generate `mywallet.pk` and save it. Never give any unauthorized party access to `mywallet.pk` as access to this file gives full access to all your funds. Rename this file for convenience to identify your wallet with a meaningful name (`mywallet` will be used as the name throughout this tutorial).
  
### 2.2. Sign deployment

  Command line: `fift -s new-wallet-v3.fif <workchain-id> <wallet-id> <wallet-filename-without-extension>`
  
  * `new-wallet-v3.fif` - the template for the wallet smart contract downloaded earlier
  * `workchain-id` - normally zero (0) as the wallet will reside on the basic workchain
  * `wallet-id` - any integer since multiple wallets can be deployed by the same key, the value `698983191` is used by standard wallets as the primary ID, using it will allow seamless import of the wallet into a third-party wallet app in the future
  * `wallet-filename-without-extension` - path to the private key file without the `.pk` extension (`mywallet.pk` will be just `mywallet`)
  
  Example: 
  ```
  fift -s new-wallet-v3.fif 0 698983191 mywallet
  ```
  
  Example output:
  ```
  Creating new advanced v3 wallet in workchain 0
  with unique wallet id 698983191
  Loading private key from file mywallet.pk
  ...
  Bounceable address (for later access): kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ
  ```
  The public wallet address in the example output is `kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ`

### 2.3. Save `mywallet.addr`

  The above command will generate an ADDR file which contains the public wallet address. The ADDR file will be created in the current directory with the name `mywallet.addr` (or any other meaningful name you chose for `wallet-filename-without-extension` above). Store the ADDR file for future use, it will be required for signing transfer transactions. This file is not secret, so it does not need heavy protection.

### 2.4. See output BOC

  The above command will generate a BOC file which contains the signed transaction for transmission to TON mainnet. The BOC file will be created in the current directory with the name `mywallet-query.boc` (or any other meaningful name you chose for `wallet-filename-without-extension` above).
  
## Step 3: Move BOC and send

### 3.1. Move BOC

  The output BOC should be moved to a regular (online) computer that is connected to the Internet. It is your responsibility to move it securely using any method like a USB-Key or QR code reader and make sure that no other important files (like `mywallet.pk`) get compromised in the process.
  
### 3.2. Fund address

  Send some TON coins to the public wallet address to fund the contract deployment. An amount of 0.1 TON should suffice. The public wallet address appears in the output of the `fift` command in step 2.
  
### 3.3. Prepare `lite-client`

  The lite client command line tool is another part of the TON toolchain. Its source code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Build it from source code by following the [official instructions](https://ton.org/docs/#/compile?id=lite-client) or find a trusted source for [pre-built binaries](https://github.com/ton-defi-org/ton-binaries) that you can just download and use.
  
  Download the latest TON mainnet configuration file from https://newton-blockchain.github.io/global.config.json.

  Example:
  ```
  cp ~/ton/build/lite-client/lite-client ~/ton-offline
  wget https://newton-blockchain.github.io/global.config.json -P ~/ton-offline
  ```

### 3.4. Send BOC to mainnet

  Command line: `lite-client -C global.config.json -c 'sendfile <boc-file>'`
  
  * `global.config.json` - TON mainnet configuration file downloaded earlier
  * `boc-file` - path to the BOC file to send
  
  Example: 
  ```
  lite-client -C global.config.json -c 'sendfile mywallet-query.boc'
  ```
  
  Example output:
  ```
  using liteserver 6 with addr [51.195.176.148:4194]
  [ 1][t 1][2022-03-28 11:18:26.184795][lite-client.h:362][!testnode]	conn ready
  [ 2][t 1][2022-03-28 11:18:26.203706][lite-client.cpp:363][!testnode]	server version is 1.1, capabilities 7
  [ 3][t 1][2022-03-28 11:18:26.203748][lite-client.cpp:372][!testnode]	server time is 1648466306 (delta 0)
  ...
  [ 1][t 1][2022-03-28 11:18:26.243543][lite-client.cpp:1150][!testnode]	sending query from file mywallet-query.boc
  [ 3][t 1][2022-03-28 11:18:26.261124][lite-client.cpp:1160][!testnode]	external message status is 1
  ```

## Step 4: Sign transfer offline

### 4.1. Ready `mywallet.pk`

  The primary secret stored on the offline computer is the private key file. If you only have a 24 word mnemonic (BIP39), open `ton-mnemonic-pk.html` using a web browser (offline), type the mnemonic into the page and use it to generate `mywallet.pk` and save it. Never give any unauthorized party access to `mywallet.pk` as access to this file gives full access to all your funds. Rename this file for convenience to identify your wallet with a meaningful name (`mywallet` will be used as the name throughout this tutorial).
  
### 4.2. Ready `mywallet.addr`

  This file was generated in step 2 when the wallet contract deployment was created. You should have stored this file for future use. This file is not secret, so it does not need heavy protection. If you lost this file, repeat step 2 to create it again (just make sure to use the same arguments you used on the original execution, specifically the wallet ID and the workchain ID).
  
### 4.3. Sign deployment

  Command line: `fift -s wallet-v3.fif <wallet-filename-without-extension> <destination-address> <wallet-id> <seq-no> <ton-coin-amount> <boc-output-file> --timeout 86400`
  
  * `wallet-v3.fif` - the template for the wallet smart contract downloaded earlier
  * `wallet-filename-without-extension` - path to the private key file without the `.pk` extension and the address file without the `.addr` extension (`mywallet.pk` and `mywallet.addr` will be just `mywallet`)
  * `destination-address` - the public wallet address you want to send TON coins to
  * `wallet-id` - the wallet ID used when the wallet was deployed, the value `698983191` is used by standard wallets as the primary ID
  * `seq-no` - the number of external transactions sent to the wallet, can be seen on a block explorer or checked via lite client (in an online computer) by running `lite-client -C global.config.json -c 'runmethod kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ seqno'` using the wallet public address
  * `ton-coin-amount` - the decimal amount of TON coins to send, meaning `0.1` to send 0.1 TON, note that you cannot send the entire wallet balance and must leave around 0.01 TON in the wallet for gas
  * `boc-output-file` - the name of the output BOC file that will be created in the current directory
  * `timeout` - timeout in seconds for sending the BOC to mainnet (default is 60 seconds which is too low)
  
  Example: 
  ```
  fift -s wallet-v3.fif mywallet EQDrjaLahLkMB-hMCmkzOyBuHJ139ZUYmPHu6RRBKnbdLIYI 698983191 17 0.03 mywallet-tx17 --timeout 86400
  ```
  
  Example output:
  ```
  Source wallet address = 0:697c29a97efc9b8a608a32a60e52f2e6ba8635d55af25e17668ec3fc311bc03b
  kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ
  Loading private key from file mywallet.pk
  Transferring GR$0.03 to account kQDrjaLahLkMB-hMCmkzOyBuHJ139ZUYmPHu6RRBKnbdLD2C = 0:eb8da2da84b90c07e84c0a69333b206e1c9d77f5951898f1eee91441
  ...
  Query expires in 86400 seconds
  (Saved to file mywallet-tx17.boc)
  ```

### 4.4. See output BOC

  The above command will generate a BOC file which contains the signed transaction for transmission to TON mainnet. The BOC file will be created in the current directory with the name `mywallet-tx17.boc` (or what name you chose for `boc-output-file` above).

## Step 5: Move BOC and send

### 5.1. Move BOC

  The output BOC should be moved to a regular (online) computer that is connected to the Internet. It is your responsibility to move it securely using any method like a USB-Key or QR code reader and make sure that no other important files (like `mywallet.pk`) get compromised in the process.
    
### 5.2. Prepare `lite-client`

  See explanation under step 3.

### 5.3. Send BOC to mainnet

  Command line: `lite-client -C global.config.json -c 'sendfile <boc-file>'`
  
  * `global.config.json` - TON mainnet configuration file downloaded earlier
  * `boc-file` - path to the BOC file to send
  
  Example: 
  ```
  lite-client -C global.config.json -c 'sendfile mywallet-tx17.boc'
  ```
  
  Example output:
  ```
  using liteserver 6 with addr [51.195.176.148:4194]
  [ 1][t 1][2022-03-28 11:18:26.184795][lite-client.h:362][!testnode]	conn ready
  [ 2][t 1][2022-03-28 11:18:26.203706][lite-client.cpp:363][!testnode]	server version is 1.1, capabilities 7
  [ 3][t 1][2022-03-28 11:18:26.203748][lite-client.cpp:372][!testnode]	server time is 1648466306 (delta 0)
  ...
  [ 1][t 1][2022-03-28 11:18:26.243543][lite-client.cpp:1150][!testnode]	sending query from file mywallet-tx17.boc
  [ 3][t 1][2022-03-28 11:18:26.261124][lite-client.cpp:1160][!testnode]	external message status is 1
  ```
