---
layout: page
title: Inbound Bitcoin Transaction
---

Before going any further, make sure you have a testnet Wanchain node and a
testnet Bitcoin node running, and that you have a funded Wanchain account and a
funded Bitcoin address. With those in place, we are ready to set up a Bitcoin
cross-chain transaction.

## Getting started

To get started, let's create a new directory and initialize npm in it, and then
add the dependencies we will be using in our example scripts.

```bash
$ mkdir wanchain-bitcoin-crosschain
$ cd !$
$ npm init
$ npm install --save wanx web3 keythereum wanchainjs-tx node-bitcoin-rpc moment bignumber.js
```

Before setting up our transaction script, let's add a file (`./utils.js`) that
includes the Wanchain helper function introduced before with the Ethereum
integration, but now also with another utility function for sending bitcoin to
a given address. We'll use this `sendBtc` function to add funds to the P2SH
lock address that we create.

**utils.js**
```js
const WanTx = require('wanchainjs-tx');

module.exports = {
  sendBtc,
  sendRawWanTx,
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

async function sendBtc(bitcoinRpc, toAddress, toAmount, changeAddress) {

  const rawTx = await callRpc(bitcoinRpc, 'createrawtransaction', [[], { [toAddress]: toAmount }]);

  const fundArgs = { changePosition: 1 };

  if (changeAddress) {
    fundArgs.changeAddress = changeAddress;
  }

  const fundedTx = await callRpc(bitcoinRpc, 'fundrawtransaction', [rawTx, fundArgs]);

  const signedTx = await callRpc(bitcoinRpc, 'signrawtransactionwithwallet', [fundedTx.hex]);

  const txid = await callRpc(bitcoinRpc, 'sendrawtransaction', [signedTx.hex]);

  return txid;
}

async function sendRawWanTx(web3, rawTx, fromAccount, privateKey) {

  // Get the tx count to determine next nonce
  const txCount = await web3.eth.getTransactionCount(fromAccount);

  // Add the nonce to tx
  rawTx.nonce = web3.utils.toHex(txCount);

  // Sign and serialize the tx
  const transaction = new WanTx(rawTx);
  transaction.sign(privateKey);
  const serializedTx = transaction.serialize().toString('hex');

  // Send the lock transaction on Ethereum
  const receipt = await web3.eth.sendSignedTransaction('0x' + serializedTx);

  return receipt;
}
```

The `sendBtc` function creates a funded raw transaction with the change address
in the second output. ().

<div class="alert alert-info">
  <b>Note</b>: For Storeman groups, a Bitcoin lock transaction is valid only if
  the output that funds the P2SH address is the first output in the
  transaction.
</div>

## Make the transaction

Now let's start on our script that will make the inbound Bitcoin cross-chain
transaction, `btc2wbtc.js`. To remind you, this script will convert bitcoin
(BTC) to the bitcoin token on Wanchain (wBTC), and it will do so by following
the steps previously mentioned.

To start, add the necessary dependencies to the top of the script.

**btc2wbtc.js**
```js
const WanX = require('wanx');
const Web3 = require('web3');
const keythereum = require('keythereum');
const bitcoinRpc = require('node-bitcoin-rpc');
const BigNumber = require('bignumber.js');
const moment = require('moment');

const utils = require('./utils');
```

Next, set up an RPC connection with the bitcoin node, making sure to fill in
the correct address, port, and RPC username and password.  Also, since the
default timeout for the bitcoin RPC package is quite low and can cause
problems, go ahead and increase that to 2 seconds.

```js
const btcNode = [ 'localhost', 18332, '<btcuser>', '<btcpassword>' ];

bitcoinRpc.init(...btcNode);
bitcoinRpc.setTimeout(2000);
```

Then set up WanX, as well as Web3, which like before we'll use for
communicating with the Wanchain node.

```js
const config = {
  wanchain: { url: 'http://localhost:18545' },
};

const wanx = new WanX('testnet', config);

const web3wan = new Web3(new Web3.providers.HttpProvider(config.wanchain.url));
```

For the final piece of the setup, let's use keythereum and unlock the Wanchain
account keystore. Make sure to put in the correct Wanchain address, the
correct path to the keystore file, as well as the correct keystore passphrase.

```js
const wanAddress = '<myWanchainAddress>';

const wanDatadir = '/home/<myuser>/.wanchain/testnet/';
const wanKeyObject = keythereum.importFromFile(wanAddress, wanDatadir);
const wanPrivateKey = keythereum.recover('mypassword', wanKeyObject);
```

Now we are ready to initialize a new wanx chain and start our cross-chain
transaction. First, let's set up the new chain with wanx, using the `newChain`
method. The 1st argument specifies the chain we want to connect with, in this
case "btc", and the 2nd argument indicates that this will be an inbound
transaction (`true` for inbound, and `false` for outbound).

```js
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

Now add the revoker address to our `btc2wbtc.js` script.

```js
const revokerAddress = 'mvTfNujpcQwHaefMxfJRix4vhfNBxSFbBe';
```

At last, let's define the transaction options.

```js
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

Before the transaction gets kicked off, let's also log out the transaction
`opts`. In the crude example the `redeemKey` is not stored anywhere, so we need
to make sure to print it to stdOut so that we can capture the `redeemKey`, in
case we need to redeem or revoke later.

```js
console.log('Tx opts:', opts)
```

<div class="alert alert-info">
  If the script runs perfectly you will not need to know or keep the
  <code>redeemKey</code>. But if the scripts has an error and the transaction
  does not complete, you will not be able to recover any lost funds without the
  <code>redeemKey</code>.
</div>

Next, let's go ahead and add in the basic logic of the transaction. We'll fill
in the missing functions in a bit.

```js
Promise.resolve([])
  .then(lockBitcoin)
  .then(sendLock)
  .then(confirmLock)
  .then(sendRedeem)
  .then(confirmRedeem)
  .catch(err => {
    console.log('Error:', err);
  });
```

Let's go ahead and define the functions for these five steps.

```js
async function lockBitcoin() {

  1.md

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
  const txid = await btcUtils.sendBtc(bitcoinRpc, contract.address, sendAmount);

  // Add txid to opts
  opts.txid = txid;

  console.log('BTC tx sent', txid);
}

async function sendLock() {

  // Get the raw lock tx
  const lockTx = cctx.buildLockTx(opts);

  // Send the lock tx on Ethereum
  const receipt = await utils.sendRawWanTx(web3eth, lockTx, opts.to, wanPrivateKey);

  console.log('Lock sent:', receipt);
}

async function confirmLock() {

  // Get the current block number on Wanchain
  const blockNumber = await web3wan.eth.getBlockNumber();

  // Scan for the lock confirmation from the storeman
  const log = await cctx.listenLock(opts, blockNumber);

  console.log('Lock confirmed:', log);
}

async function sendRedeem() {

  // Get the raw redeem tx
  const redeemTx = cctx.buildRedeemTx(opts);

  // Send the redeem transaction on Wanchain
  const receipt = await utils.sendRawWanTx(web3wan, redeemTx, opts.to, wanPrivateKey);

  console.log('Redeem sent:', receipt);
}

async function confirmRedeem(receipt) {

  // Scan for the lock confirmation from the storeman
  const log = await cctx.listenRedeem(opts, receipt.blockNumber);

  console.log('Redeem confirmed:', log);
  console.log('COMPLETE!!!');
}
```

As you can see, the `sendLock` function generates a new P2SH lock address
(`buildHashTimeLockContract`) and sends bitcoin to it (`sendBtc`). The
`lockTime` and `txid` are saved to `opts`, as they are needed parameters in the
next step where we send a lock notice contract call on Wanchain
(`buildLockTx`). Once the lock notice is sent, it waits for a response from the
storeman (`listenLock`). After the Storeman group confirms the lock, it sends a
redeem call on Wanchain. And then finally, it waits for the Storeman group to
confirm the redeem.

If everything went well, the Wanchain account used in the `opts` should now
have 0.0021 Bitcoin token. The token is a WRC-20 token, so you can make
the usual token contract calls, such as `balanceOf`, `transfer`,
`transferFrom`, etc. Alternatively, you can check the explorer site to that the
transaction completed and that the Wanchain account has Bitcoin token.
