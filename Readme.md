### Sending Transaction from a Tails computer

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
