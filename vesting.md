# Using Vesting Contact on a black computer 


## What you need 
- A vesting contract address <vesting_contract_address>
- private key file <mywallet.pk> generated previously using ton-mnemonic-pk.html
- <destination> address - where you want to send funds to 
- <ton_amount> - amount of TONs to send
- $seqno  


## get seqno <seqno>
Get Current SeqNo for vesting Wallet
To sign the message, you must input the correct seqno of the signer otherwise it’s invalid.
Please get the seqno by visiting (on a regular device) https://tonwhales.com/explorer/address/<wallet_address>
```
curl -X 'GET' \ 'https://toncenter.com/api/v2/getWalletInformation?address=<wallet_address>' \ -H 'accept: application/json'
```


## Generate .addr file for vesting account
in order to generate .addr file use this command , the *vesting_contract_address* is the address of the vesting contract.
`fift -s str-to-addr.fif <vesting_contract_address>`
this operation will create mywallet.addr file 

## Use Simple Transfer with newly generated .addr file and the private key file

Example: 
  ```
  ./fift -s wallet-v3.fif mywallet <vesting_contract_address> 698983191 <seqno> <ton_amount> boc-query --timeout 86400
  ```

## Publish the boc using QR code 
1. open boc-to-qr.html in a browser
2. upload boc-query.boc to the page
3. scan the QR code with your phone
4. verify the params and addresses , and click publish