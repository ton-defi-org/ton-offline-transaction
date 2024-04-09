# Using Vesting Contact on a black computer 


## What you need 
- A vesting contract address <vesting_contract_address>
- private key file <mywallet.pk> generated previously using ton-mnemonic-pk.html
- <destination> address - where you want to send funds to 
- <ton_amount> - amount of TONs to send
- < seqno > - vesting wallets seqno (documented below)  


## get seqno <seqno>
Get Current SeqNo for vesting Wallet
To sign the message, you must input the correct seqno of the signer otherwise it’s invalid.
Please get the seqno by visiting (on a regular device) https://verifier.ton.org/<wallet_address>
go to Getters tab and run `seqno()` method to get the current seqno of the wallet (<seqno>).

or use a curl 
vesting_contract_address_decimals_format = 0:b21c74F113C504144d25BEC6FFA5089ED79a2d6f //use this tool https://ton.org/address
```
curl 'https://ton.access.orbs.network/b21c74F113C504144d25BEC6FFA5089ED79a2d6f/1/mainnet/toncenter-api-v2/jsonRPC'   -H 'x-ton-client-version: 11.19.0'   --data-raw '{"id":"1","jsonrpc":"2.0","method":"runGetMethod","params":{"address":"<vesting_contract_address_decimals_format>","method":"seqno","stack":[]}}'   --compressed
```


## Generate .addr file for vesting account
in order to generate .addr file use this command , the *vesting_contract_address* is the address of the vesting contract.
`fift -s str-to-addr.fif <vesting_contract_address>`
this operation will create mywallet.addr file 

## Use Simple Transfer with newly generated .addr file and the private key file

Example: 
  ```
  ./fift -s wallet-v3.fif mywallet <vesting_contract_address> 268 <seqno> <ton_amount> boc-query --timeout 86400
  ```

## Publish the boc using QR code 
1. open boc-to-qr.html in a browser
2. upload boc-query.boc to the page
3. scan the QR code with your phone
4. verify the params and addresses , and click publish
