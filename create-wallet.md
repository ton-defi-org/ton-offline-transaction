### Generating new wallet 
- generate pk file by clicking on generate button in mnemonic2pk.html tool and download <key.pk>.
- use file [new-wallet-v3.fif](https://github.com/newton-blockchain/ton/blob/master/crypto/smartcont/new-wallet-v3.fif)
- execute command `/fift -s new-wallet-v3.fif  <workchain-id> <wallet-id> <key.pk>` to generate .boc file that deploys the new wallet.