# Send Offline Transactions for TON Blockchain

## Why?

Your wallet private key / 24 word mnemonic is the main secret you must protect in order to secure your funds from theft. The most secure way to protect secrets is via cold storage - meaning hold the secret on a "cold" device that isn't connected to the Internet. By completely disconnecting the secret from the outside world, no hacker could gain access to it.

This is often achieved through the use of hardware wallets (like Trezor and Ledger) that hold the secret and prevent it from being accessed. Unfortunately, the popular hardware wallets don't support TON yet officially. The protocol defined in this repo is similar in the sense that a special offline computer assumes the role of the hardware wallet.

## The workflow

#### Setup offline wallet

1. Setup a special offline computer - this computer will hold your TON private key and should never be connected to the Internet.
2. To deploy your TON wallet contract, go to the offline computer and sign the deploy transaction (generate a BOC file).
3. Move the BOC file to a different online computer (via USB-Key, QR code, etc) and transmit it to TON mainnet using a lite client.

#### Sending TON coins

4. If you want to send TON coins, go to the offline computer and sign a new send transaction (generate a BOC file).
5. Move the BOC file to a different online computer (via USB-Key, QR code, etc) and transmit it to TON mainnet using a lite client.

## Step 1: Setup offline computer

This computer should never be connected to the Internet, so all tools must be installed on it in advance:

* Build `fift` executable

  This command line tool is part of the TON toolchain. Its source code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Build it from source code by following the [official instructions](https://ton.org/docs/#/compile?id=fift) or find a trusted source for pre-built binaries that you can just download and use.
  
* Download `fift-lib` library
  
  This dependency is a standard collection of about 8 fift files. They are part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download the files from https://github.com/newton-blockchain/ton/tree/master/crypto/fift/lib.
  
* Download `wallet-v3.fif` and `new-wallet-v3.fif`

  The TON core team publishes the official template for a wallet smart contract. In the time of writing, the most recent version is wallet V3. The code is part of the official [TON repo](https://github.com/newton-blockchain/ton/). Download [wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3.fif) and [new-wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/new-wallet-v3.fif) from https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont.

* Build `tonweb-mnemonic.js` JS library

  This JavaScript library helps manipulate 24 word mnemonics (BIP39) and convert them to functional private keys that can sign TON transactions. The library source code is available [here](https://github.com/toncenter/tonweb-mnemonic). Build it from source code by running `npm install && npm run build:web && cp dist/web/index.js tonweb-mnemonic.js` or find a trusted source for a pre-built version that you can just download and use. 

* Download `ton-mnemonic-pk.html`

  This offline HTML relies on `tonweb-mnemonic.js` and provides an easy-to-use interface where you can convert an existing 24 word mnemonic (BIP39) to a TON-compatible private key file (`key.pk`). The private key file (`key.pk`) is needed as argument to `fift` executable for signing transactions. The HTML is part of this repo and can be downloaded [here](ton-mnemonic-pk.html).

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
