---
layout: page
title: Inbound Bitcoin Transaction
---

At this point you should have a testnet Wanchain node and a testnet Bitcoin
node running, and you should have a funded Wanchain account and a funded
Bitcoin address. With those in place, we are now ready to set up our first
cross-chain transactions. To do this, we will use the `wanx` npm package.

## Getting set up

### Set up for development

To get started, let's create a new directory and initialize npm in it.

```
$ mkdir crosschain-test
$ cd crosschain-test
$ npm init
```

Then let's add the dependencies we will be using in our example scripts.

```
$ npm install --save wanx web3 wanchainjs-tx keythereum node-bitcoin-rpc moment bignumber.js
```

Before setting up our transaction script, let's add a file (`./btc-utils.js`)
with a utility function for sending bitcoin to a given address. We'll use this
`sendBtc` function to add funds to the lock address that we create.

**btc-utils.js**
```javascript
module.exports = {
  sendBtc,
};

function callRpc(bitcoinRpc, method, args) {
  return new Promise((resolve, reject) => {
    bitcoinRpc.call(method, args, (err, res) => {
      if (err) {
        return reject(err);
      } else if (res.error) {
        return reject(res.error);
      }

      resolve(res.result);
    });
  });
}

function sendBtc(bitcoinRpc, toAddress, toAmount, changeAddress) {
  return Promise.resolve([]).then(() => {

    return callRpc(bitcoinRpc, 'createrawtransaction', [[], { [toAddress]: toAmount }]);

  }).then(rawTx => {

    const fundArgs = { changePosition: 1 };

    if (changeAddress) {
      fundArgs.changeAddress = changeAddress;
    }

    return callRpc(bitcoinRpc, 'fundrawtransaction', [rawTx, fundArgs]);

  }).then(fundedTx => {

    return callRpc(bitcoinRpc, 'signrawtransactionwithwallet', [fundedTx.hex]);

  }).then(signedTx => {

    return callRpc(bitcoinRpc, 'sendrawtransaction', [signedTx.hex]);

  });
}

```

## Making an inbound bitcoin transaction

Now let's start on our script that will make the inbound Bitcoin cross-chain
transaction, which we will call `btc-inbound.js`. To remind you, this script
will convert bitcoin (BTC) to the bitcoin token on Wanchain (wBTC), and it will
do so by following the four steps listed above.

To start, add the necessary imports to the top of the script.

**btc-inbound.js**
```javascript
const WanX = require('wanx');
const Web3 = require('web3');
const keythereum = require('keythereum');
const WanTx = require('wanchainjs-tx');
const moment = require('moment');
const bitcoinRpc = require('node-bitcoin-rpc');
const BigNumber = require('bignumber.js');

const btcUtils = require('./btc-utils');
```

Next, set up an RPC connection with the bitcoin node.

```javascript
const btcNode = [ 'localhost', 18332, 'btcuser', 'btcpassword' ];

bitcoinRpc.init(...btcNode);
bitcoinRpc.setTimeout(2000);
```

Then set up WanX, as well as Web3, which we'll use for communicating with the
Wanchain node.

```javascript
const config = {
  wanchain: { url: 'http://localhost:18545' },
};

const wanx = new WanX('testnet', config);

const web3wan = new Web3(new Web3.providers.HttpProvider(config.wanchain.url));
```

For the final piece of the setup, let's use keythereum and unlock the Wanchain
account keystore. Make sure to put in the correct Wanchain address, the
correct path to the keystore file, as well as the correct keystore passphrase.

```javascript
const wanAddress = '<myWanchainAddress>';

const wanDatadir = '/home/<myuser>/.wanchain/testnet/';
const wanKeyObject = keythereum.importFromFile(wanAddress, wanDatadir);
const wanPrivateKey = keythereum.recover('mypassword', wanKeyObject);
```

Now we are ready to initialize a new wanx chain and start our cross-chain
transaction. First, let's set up a new chain with wanx, using the `newChain`
method. The 1st argument specifies the chain we want to connect with, in this
case "btc", and the 2nd argument indicates that this will be an inbound
transaction (`true` for inbound, and `false` for outbound).

```javascript
// bitcoin, inbound
const cctx = wanx.newChain('btc', true);
```

Our final setup action will be to define the transaction options. Each chain
type requires certain parameters to be passed within the options, which can be
deduced from the [WanX Documentation](https://wanchain/wanx).

In the options we need to define the Bitcoin address that will be the revoker
address. The revoker address must be a legacy address, and it does not need to
be funded. Thus, we will create a new legacy bitcoin address for the sole
purpose of revoking.

```bash
$ bitcoin-cli -testnet getnewaddress '' legacy
```

Now add the revoker address to our `btc-inbound.js` script.

```javascript
const revokerAddress = 'mvTfNujpcQwHaefMxfJRix4vhfNBxSFbBe';
```

At last, let's define the transaction options.

```javascript
const opts = {
  // Revoker bitcoin address
  from: revokerAddress,

  // Recipient wanchain address
  to: wanAddress,

  // Value in satshis
  value: '210000',

  // Storeman group addresses
  storeman: {
    wan: '0x9ebf2acd509e0d5f9653e755f26d9a3ddce3977c',
    btc: '0x83e5ca256c9ffd0ae019f98e4371e67ef5026d2d',
  },

  // Generate a new redeemKey
  redeemKey: wanx.newRedeemKey('sha256'),
};
```

The options include the revoker address, the Wanchain address, the amount to be
sent (210000 satoshis), the addresses of the Storeman group, and a new
redeemKey. The redeemKey includes two parts, a random string (`x`, which is the
key needed to redeem the token) and the hash of the random string (`xHash`,
which is the transaction identifier). For the case of Bitcoin, we need the
redeemKey hash to be a SHA256 hash.

With our transaction options defined, we are finally at the point where we can
actually start the transaction. The following snippet kicks off the transaction
and runs through all of the required four steps, using a chain of promises.

```javascript
Promise.resolve([]).then(() => {

  // Step 1a: generate a new P2SH lock address and send bitcoin to it

  // Log our options
  console.log('Starting btc inbound lock', opts);

  // Create new P2SH lock address
  const contract = cctx.buildHashTimeLockContract(opts);

  // Log the P2SH address details
  console.log('Created new BTC contract', contract);

  // Add the lockTime to opts
  opts.lockTime = contract.lockTime;

  // Convert BTC amount from satoshis to bitcoin
  const sendAmount = (new BigNumber(opts.value)).div(100000000).toString();

  console.log('Send amount', sendAmount);

  // Send BTC to P2SH lock address
  return btcUtils.sendBtc(bitcoinRpc, contract.address, sendAmount);

}).then(txid => {

  // Step 1b: save the txid of the funding transaction

  // Add txid to opts
  opts.txid = txid;

  console.log('BTC tx sent', txid);

  // Get the tx count to determine next nonce
  return web3wan.eth.getTransactionCount(opts.to);

}).then(txCount => {

  // Step 1c: send the lock notice on Wanchain

  // Construct the raw lock tx
  const lockTx = cctx.buildLockTx(opts);

  // Add nonce to tx
  lockTx.nonce = web3wan.utils.toHex(txCount);

  // Sign and serialize the tx
  const transaction = new WanTx(lockTx);
  transaction.sign(wanPrivateKey);
  const serializedTx = transaction.serialize().toString('hex');

  // Send the lock transaction on Wanchain
  return web3wan.eth.sendSignedTransaction('0x' + serializedTx);

}).then(receipt => {

  // Step 2: wait for the Storeman group to confirm the lock

  console.log('Lock submitted and now pending on storeman');
  console.log(receipt);

  // Scan for the lock confirmation from the storeman
  return cctx.listenLock(opts, receipt.blockNumber);

}).then(log => {

  console.log('Lock confirmed by storeman');
  console.log(log);

  // Get the tx count to determine next nonce
  return web3wan.eth.getTransactionCount(opts.to);

}).then(txCount => {

  // Step 3: make Wanchain contract call to redeem the wBTC token

  // Get the raw redeem tx
  const redeemTx = cctx.buildRedeemTx(opts);

  // Add nonce to tx
  redeemTx.nonce = web3wan.utils.toHex(txCount);

  // Sign and serialize the tx
  const transaction = new WanTx(redeemTx);
  transaction.sign(wanPrivateKey);
  const serializedTx = transaction.serialize().toString('hex');

  // Send the lock transaction on Wanchain
  return web3wan.eth.sendSignedTransaction('0x' + serializedTx);

}).then(receipt => {

  // Step 4: wait for the Storeman group to confirm the redeem

  console.log('Redeem submitted and now pending on storeman');
  console.log(receipt);

  // Scan for the lock confirmation from the storeman
  return cctx.listenRedeem(opts, receipt.blockNumber);

}).then(log => {

  console.log('Redeem confirmed by storeman');
  console.log(log);
  console.log('COMPLETE!!!');

}).catch(err => {

  console.log('Error:', err);

});
```

As you can see, the script starts by generating a new P2SH lock address
(`buildHashTimeLockContract`) and sending bitcoin to it (`sendBtc`). The
`lockTime` and `txid` are saved to `opts`, as they are needed parameters in the
next step where we send a lock notice contract call on Wanchain
(`buildLockTx`). Once the lock notice is sent, it waits for a response from the
storeman (`listenLock`). After the Storeman group confirms the lock, it then
sends a redeem call on Wanchain (`buildRedeemTx`), and then finally waits for
the Storeman group to confirm the redeem (`listenRedeem`).

If everything went well, the Wanchain account used in the `opts` should now
have 0.0021 wBTC token. The wBTC token is a WRC-20 token, so you can make
the usual token contract calls, such as `balanceOf`, `transfer`,
`transferFrom`, etc. Accordingly, you can check the token balance at the
console.

```bash
# Attach the console
$ ./gwan attach http://localhost:18545

>
```
