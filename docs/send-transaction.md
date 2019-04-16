---
layout: page
author: tyrion70
title: Send a Transaction
---

With a Wanchain created, we are ready to receive funds and make transactions. Before any transactions can be made, you'll need to acquire some coins. If you do not have any already, you can get some testnet coins from the [Wanchain Faucet](https://faucet1.wanchain.org).

Now with coins in hand, let's make some transactions.

### At the Console

A simple way to send a one-off transaction is by issuing the `sendTransaction`
command in the console. Note that to do this you will initially need to unlock
the sending account, with the `personal.unlockAccount()` command.

```js
> var from = eth.accounts[0]
> var to = "0x184bfe537380d650533846c8c7e2a80d75acee63"
> var value = web3.toWei('1')
> web3.eth.sendTransaction({
       from: from,
       to: to,
       value: value,
   }, function(err, txHash) {
       if (err) {
           console.log(err);
       } else {
           console.log(txHash);
       }
   });
```

### Web3 with Unlocked Account

The code run in the console could be extracted and run as a separate script. An
early-stage Dapp might take this approach at first since it allows programs to
interact with the network without the overhead of key management.

**send-tx.js**
```js
const Web3 = require('web3');
const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:18545'));

const from = '0x68489694189aa9081567dfc6d74a08c0c21d92c6';
const to = '0x184bfe537380d650533846c8c7e2a80d75acee63';
const value = '1000000000000000000';

web3.eth.sendTransaction({ from, to, value }).then(receipt => {
  console.log(receipt);
}).catch(err => {
  console.log(err);
});
```

Now we can run this from the command line with the following.

```bash
$ node send-tx.js
```

### Sign with Wanchainjs-tx

It's generally not a good idea to interact with an unlocked account. Instead, for interactions with a specific non-user account, you should access the private key independently and sign the transaction with the key before sending it to the network. One library that helps with transaction signing is `Wanchainjs-tx`.

<div id="runkit-element" class="runkit-element">
<code></code>
<code>
const Web3 = require('web3')
const WanchainTx = require('wanchainjs-tx')
const privateKey = Buffer.from('e331b6d69882b4cb4ea581d88e0b604039a3de5967688d3dcffdd2270c0fd109', 'hex')
const web3 = new Web3(new Web3.providers.HttpProvider('http://localhost:18545'));

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

// sign the transaction
const tx = new WanchainTx(txParams)
tx.sign(privateKey)
console.log(tx.getSenderAddress().toString('hex'))

// get the serialized signed transaction
const serializedTx = '0x' + tx.serialize().toString('hex')
console.log(serializedTx)

// send the signed transaction to the network
web3.eth.sendSignedTransaction(serializedTx)
.on('receipt', console.log);
</code>
</div>

### Send through iWan

If you are using `iWan`, Wanchain's hosted solution, you can use the Javascript SDK to send a transaction.

```js
const iWanClient = require('iwan-sdk');
const WanchainTx = require('wanchainjs-tx')

const apiClient = new iWanClient('YourApiKey', 'YourSecretKey');
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

// sign the transaction
const tx = new WanchainTx(txParams)
tx.sign(privateKey)
console.log(tx.getSenderAddress().toString('hex'))

// get the serialized signed transaction
const serializedTx = '0x' + tx.serialize().toString('hex')
console.log(serializedTx)

apiClient.sendRawTransaction('WAN', serializedTx, (err, receipt) => {
  if (err) {
    console.log(err);
  } else {
    console.log(receipt);
  }

  // make sure to close when you are done
  apiClient.close();
});
```
