---
layout: page
author: tyrion70
title: Send a Transaction
---

This document describes how to send a transaction for Wanchain programmatically:

### Console
To simply send a transaction using the console use this command:

```js
web3.eth.sendTransaction({
       from: "0x68489694189aa9081567dfc6d74a08c0c21d92c6",
       to: "0x184bfe537380d650533846c8c7e2a80d75acee63",
       value: 1000000000000000,
   }, function(err, transactionHash) {
       if (err) {
           console.log(err);
       } else {
           console.log(transactionHash);
       }
   });
```

### Wanchainjs-tx
<div id="runkit-element" class="runkit-element">
<code></code>
<code>
const web3 = require('wan3')
const WanchainTx = require('wanchainjs-tx')
const privateKey = Buffer.from('e331b6d69882b4cb4ea581d88e0b604039a3de5967688d3dcffdd2270c0fd109', 'hex')

const txParams = {
  Txtype: '0x01',
  nonce: '0x00',
  gasPrice: '0x2a600b9c00',
  gasLimit: '0x5208',
  to: '0x68489694189Aa9081567dFc6D74A08c0c21D92c6',
  value: '100000000000000000',
  data: '0x0',
  // EIP 155 chainId - mainnet: 1, testnet: 3, privatenet: 99, devnet: 1337
  chainId: 99
}

const tx = new WanchainTx(txParams)
tx.sign(privateKey)
console.log(tx.getSenderAddress().toString('hex'))
const serializedTx = tx.serialize().toString('hex')
console.log(serializedTx)
web3.eth.sendSignedTransaction('0x' + serializedTx.toString('hex'))
.on('receipt', console.log);
</code>
</div>
