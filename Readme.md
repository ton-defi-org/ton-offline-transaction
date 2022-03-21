### Sending Transaction from a Tails computer

#### perquisites

1. `fift` executable you can download fift from releases
2. set `FIFTPATH=bin/fift-lib` env variable, ton folder should be cloned from [ton](https://github.com/newton-blockchain/ton)
3. [wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/wallet-v3.fif)
4. private key file <pkfile.pk>

#### Build Boc
1. run the command `fift -s wallet-v3.fif <pk_file> <destination_address> <sub_wallet_id> <seq_no> <ton_amounr(decimal)> <boc_output_file>`
2. copy boc file to a different computer with network


#### deploy boc file

- run `./lite-client -C global.config.json -c 'sendfile boc_output_file.boc'` and verify using a block explorer
