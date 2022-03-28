# Send Offline Transactions for TON Blockchain

## Why?

Your wallet private key / 24 word mnemonic is the main secret you must protect in order to secure your funds from theft. The most secure way to protect secrets is via cold storage - meaning hold the secret on a "cold" device that isn't connected to the Internet. By completely disconnecting the secret from the outside world, no hacker could gain access to it.

This is often achieved through the use of hardware wallets (like Trezor and Ledger) that hold the secret and prevent it from being accessed. Unfortunately, the popular hardware wallets don't support TON yet officially. The protocol defined in this repo is similar in the sense that a special offline computer assumes the role of the hardware wallet.

## The workflow

#### Setup offline wallet

1. **Setup offline computer** - this special computer will hold your TON private key and should never be connected to the Internet
2. **Sign deployment offline** - use the offline computer to sign a new transaction for deploying the TON wallet contract (create BOC file)
3. **Move BOC and send** - move the BOC file to a different online computer and transmit it to TON mainnet using a lite client

#### Sending TON coins

4. If you want to send TON coins, go to the offline computer and sign a new send transaction (generate a BOC file).
5. Move the BOC file to a different online computer (via USB-Key, QR code, etc) and transmit it to TON mainnet using a lite client.

## Step 1: Setup offline computer

This computer should never be connected to the Internet, so all tools must be installed on it in advance:

* **Build `fift` executable**

  This command line tool is part of the TON toolchain. Its source code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Build it from source code by following the [official instructions](https://ton.org/docs/#/compile?id=fift) or find a trusted source for pre-built binaries that you can just download and use.
  
* **Download `fift-lib` library**
  
  This dependency is a standard collection of about 8 fift files. They are part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download the files from https://github.com/newton-blockchain/ton/tree/master/crypto/fift/lib. Set the `fift-lib` directory as the environment variable `FIFTPATH` by running `export FIFTPATH=~/ton/fift-lib`. 
  
* **Download `wallet-v3.fif`, `new-wallet-v3.fif` and `wallet-v3-code.fif`**

  The TON core team publishes the official template for a wallet smart contract. In the time of writing, the most recent version is wallet V3. The code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download [wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3.fif), [new-wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/new-wallet-v3.fif) and [wallet-v3-code.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3-code.fif) from https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont.

* **Build `tonweb-mnemonic.js` JS library**

  This JavaScript library helps manipulate 24 word mnemonics (BIP39) and convert them to functional private keys that can sign TON transactions. The library source code is available [here](https://github.com/toncenter/tonweb-mnemonic). Build it from source code by running `npm install && npm run build:web && cp dist/web/index.js tonweb-mnemonic.js` or find a trusted source for a pre-built version that you can just download and use. 

* **Download `ton-mnemonic-pk.html`**

  This offline HTML relies on `tonweb-mnemonic.js` and provides an easy-to-use interface where you can convert an existing 24 word mnemonic (BIP39) to a TON-compatible private key file (`key.pk`). The private key file (`key.pk`) is needed as argument to `fift` executable for signing transactions. The HTML is part of this repo and can be downloaded [here](ton-mnemonic-pk.html).

## Step 2: Sign deployment offline

* **Ready `key.pk`**

  The primary secret stored on the offline computer is the private key file. If you only have a 24 word mnemonic (BIP39), open `ton-mnemonic-pk.html` using a web browser (offline), type the mnemonic into the page and use it to generate `key.pk` and save it. Never give any unauthorized party access to `key.pk` as access to this file gives full access to all your funds.
  
* **Sign deployment**

  Command line: `fift -s new-wallet-v3.fif <workchain-id> <wallet-id> <key-file-without-extension>`
  
  * `workchain-id` - normally zero (0) as the wallet will reside on the basic workchain
  * `wallet-id` - any integer since multiple wallets can be deployed by the same key, the value `698983191` is used by standard wallets as the primary ID, using it will allow seamless import of the wallet into a third-party wallet app in the future
  * `key-file-without-extension` - path to the private key file without the `.pk` extension (`key.pk` will be just `key`)
  
  Example: 
  ```
  fift -s new-wallet-v3.fif 0 698983191 key
  ```
  
  Example output:
  ```
  Creating new advanced v3 wallet in workchain 0
  with unique wallet id 698983191
  Loading private key from file key.pk
  ...
  Bounceable address (for later access): kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ
  ```
  The public wallet address in the example output is `kQBpfCmpfvybimCKMqYOUvLmuoY11VryXhdmjsP8MRvAO6SJ`
  
* **See output BOC**

  The above command will generate a BOC file which contains the signed transaction for transmission to TON mainnet. The BOC file will be created in the current directory with the name `key-query.boc`.
  
## Step 3: Move BOC and send

* **Move BOC**

  The output BOC should be moved to a regular (online) computer that is connected to the Internet. It is your responsibility to move it securely using any method like a USB-Key or QR code reader and make sure that no other important files (like `key.pk`) get compromised in the process.
  
* **Fund address**

  Send some TON coins to the public wallet address to fund the contract deployment. An amount of 0.1 TON should suffice. The public wallet address appears in the output of the `fift` command in step 2.
  
* **Prepare `lite-client`**

  The lite client command line tool is another part of the TON toolchain. Its source code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Build it from source code by following the [official instructions](https://ton.org/docs/#/compile?id=lite-client) or find a trusted source for pre-built binaries that you can just download and use.
  
  Download the latest TON mainnet configuration file from https://newton-blockchain.github.io/global.config.json.

* **Send BOC to mainnet**

  Command line: `lite-client -C <global.config.json> -c 'sendfile <boc-file>'`
  
  * `global.config.json` - TON mainnet configuration file downloaded earlier
  * `boc-file` - path to the BOC file to send
  
  Example: 
  ```
  lite-client -C global.config.json -c 'sendfile key-query.boc'
  ```
  
  Example output:
  ```
  using liteserver 6 with addr [51.195.176.148:4194]
  [ 1][t 1][2022-03-28 11:18:26.184795][lite-client.h:362][!testnode]	conn ready
  [ 2][t 1][2022-03-28 11:18:26.203706][lite-client.cpp:363][!testnode]	server version is 1.1, capabilities 7
  [ 3][t 1][2022-03-28 11:18:26.203748][lite-client.cpp:372][!testnode]	server time is 1648466306 (delta 0)
  ...
  [ 1][t 1][2022-03-28 11:18:26.243543][lite-client.cpp:1150][!testnode]	sending query from file key-query.boc
  [ 3][t 1][2022-03-28 11:18:26.261124][lite-client.cpp:1160][!testnode]	external message status is 1
  ```

#### perquisites

1. `fift` executable you can download fift from releases
2. set env variable by running `export FIFTPATH=bin/fift-lib` (copied from [ton](https://github.com/newton-blockchain/ton) repository)
3. [wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3.fif) - this file is a CLI tool which generates the **.boc** file
4. private key file <pkfile.pk> ( below there is a description how to use the html file to convert 24 word mnmeonic to priave key file)

#### Generating a Private key file using mnemonic
1. Open Tor Browser , navigate to mnemonic2pk.html (offline mode)
2. insert the 24 word mnemonic
3. click download button (private.pk) 

#### Build Boc
1. run the command `fift -s wallet-v3.fif <pk_file> <destination_address> <sub_wallet_id> <seq_no> <ton_amounr(decimal)> <boc_output_file>`
2. copy boc file to a computer with network access 


#### deploy boc file from a computer with network

- run `./lite-client -C global.config.json -c 'sendfile boc_output_file.boc'` and verify using a block explorer
