---
layout: page
title: Ethereum Inbound Transaction
---

Before getting started with our first cross-chain transaction, make sure that
you have both a Wanchain account and an Ethereum account. For these examples we
will be using the keystore files directly, so make sure that you know where
they are (usually in the home directory under .wanchain and .ethereum).

<div class="alert alert-info">
  <b>Note</b>: Some methods of creating Wanchain keystore files will generate a
  file named with the address in mixed upper and lower case. The
  <code>keythereum</code> package used below cannot find keystore files named
  this way. To resolve, rename the keystore file so that the address is all
  lowercase.
</div>

Also, if you haven't already, get some testnet Wanchain and Ethereum coins sent
to your accounts. You can use a faucet to get testnet coins.

#### Faucets
<table>
  <tr>
    <td>Wanchain</td>
    <td>
      <a href="https://faucet1.wanchain.org" target="_blank">
        https://faucet1.wanchain.org
      </a>
    </td>
  </tr>
  <tr>
    <td>Ethereum</td>
    <td>
      <a href="https://faucet.rinkeby.io/" target="_blank">
        https://faucet.rinkeby.io/
      </a>
    </td>
  </tr>
</table>

## Getting Started

To get things kicked off, let's start by creating a new project directory and
install some npm dependencies.

```bash
$ mkdir wanchain-crosschain-test
$ cd !$
$ npm init
$ npm install --save wanx web3 keythereum ethereumjs-tx wanchainjs-tx
```

We'll use `wanx` to build cross-chain transactions, `web3` to submit the
transactions to the Wanchain and Ethereum networks, `keythereum` to access the
private keys, and `ethereumjs-tx` and `wanchainjs-tx` to build and sign
transactions before submitting them to the network.

Now, let's create a new node script named `eth2weth.js`. This will be just a
single file script that gets executed by calling `node eth2weth.js`. At the top
of the file, let's add our dependencies.

#### eth2weth.js
```js
const WanX = require('wanx');
const Web3 = require('web3');
const keythereum = require('keythereum');
const EthTx = require('ethereumjs-tx');
const WanTx = require('wanchainjs-tx');
```

Next, let's initialize `wanx` and `web3` objects. For the sake of simplicity,
we can use Infura here for the connection to the Ethereum network.

```js
const web3wan = new Web3(new Web3.providers.HttpProvider('http://localhost:18545'));
const web3eth = new Web3(new Web3.providers.HttpProvider('https://rinkeby.infura.io/<myToken>');

const config = {
  wanchain: { web3: web3wan },
  ethereum: { web3: web3eth },
};

const wanx = new WanX('testnet', config);
```

With that in place, let's now start configuring the cross-chain transaction.
We'll do that with an objects that we'll call `opts`.

```js
const opts = {
  from: '0x4bbdfe0eb33ed498020de9286fd856f5b8331c2c',
  to: '0xa6d72746a4bb19f46c99bf19b6592828435540b0',
  value: '2101000000000000',
  storeman: {
    wan: '0x06daa9379cbe241a84a65b217a11b38fe3b4b063',
    eth: '0x41623962c5d44565de623d53eb677e0f300467d2',
  },
  redeemKey: wanx.newRedeemKey(),
}
```

In this case, since we are going from Ethereum to Wanchain, the `from` address
should be an Ethereum address and the `to` address should be a Wanchain address.
Make sure these two addresses are ones for which you have the keystore files.
The `value` is the amount of Ether that we want to send to Wanchain, priced in
Wei. The `storeman` parameter contains the Storeman group's Ethereum and
Wanchain addresses (you can leave this unchanged).

The `opts` object also requires a `redeemKey`, which can be retrieved by
calling `wanx.newRedeemKey()`. The redeemKey includes two parts, a random
string (`x`, which is the key needed to redeem the token on Wanchain) and the
hash of the random string (`xHash`, which is the transaction identifier).

Finally, before starting our transaction code, we need to set up keythereum.
First let's do this for Ethereum.

```js
const ethDatadir = '/home/<myUser>/.ethereum/testnet/';
const ethKeyObject = keythereum.importFromFile(opts.from, ethDatadir);
const ethPrivateKey = keythereum.recover('<myPassword>', ethKeyObject);
```
Make sure to put in your username for `<myUser>` and that the path correctly
points to the directory that contains the `keystore` directory where your
keystore file lives. Also, make sure to put in your keystore password in place
of `<myPassword>`.

Then, let's do the same for the Wanchain keystore.

```js
const wanDatadir = '/home/<myUser>/.wanchain/testnet/';
const wanKeyObject = keythereum.importFromFile(opts.to, wanDatadir);
const wanPrivateKey = keythereum.recover('<myPassword>', wanKeyObject);
```

Even though the code we have thus far doesn't really do anything, go ahead and
run the script. If there are any issues with accessing your keystore files, you
should see the errors here. Ideally you should see no output when running the
script as it currently stands.

```bash
$ node eth2weth.js
```

### Making the Transaction

At this point the script is sufficiently set up and we are now ready to make
the cross-chain transaction. To get this started, let's initialize a new
cross-chain transaction object.

```js
// New crosschain transaction
// ethereum, inbound
const cctx = wanx.newChain('eth', true);
```

Before the transaction gets kicked off, let's also log out the transaction `opts`. In the crude example the `redeemKey` is not stored anywhere, so we need to make sure to print it to stdOut so that we can capture the `redeemKey`, in case we need to redeem or revoke later.

<div class="alert alert-info">
  If the script runs perfectly you will not need to know or hang on to the
  <code>redeemKey</code>. But if the scripts has an error and the transaction
  does not complete, you will not be able to recover any lost funds without the
  <code>redeemKey</code>.
</div>

Here we initialized `cctx` as an inbound (inbound = true) transaction on the
Ethereum (eth) chain. Next, let's go ahead and add in the basic logic of the
transaction. We'll fill in the missing functions in a bit.

```js
Promise.resolve([])
  .then(getNonceEth)
  .then(sendLock)
  .then(printReceipt)

  .then(getBlockNumberWan)
  .then(confirmLock)
  .then(printReceipt)

  .then(getNonceWan)
  .then(sendRedeem)
  .then(printReceipt)

  .then(getBlockNumberEth)
  .then(confirmRedeem)
  .then(printReceipt);
```

The full logic of the transaction is handled by a chain of promises that flow
through the required steps of the cross-chain transaction. In addition to the 4
steps outlined previously, there are 2 additional calls to get the account
nonce, `getNonceEth` and `getNonceWan`, plus 2 other additional calls to get
the height of the current block. Also, each of the 4 steps is followed by a
`printReceipt` call.

The first functions we will define are the `getNonceEth` and `getNonceWan`
functions. All they need to do is fetch the account's last nonce from the node
(or Infura) and return it. Since in these examples we are constructing and
signing raw transactions, we have to manually retrieve the last nonce for the
account.  In other situations you may not need to make any additional calls to
fetch the nonce, like if you are iteracting directly with a node with unlocked
accounts.


```js
function getNonceEth() {

  // Get the tx count to determine next nonce
  return web3eth.eth.getTransactionCount(opts.from);

}

function getNonceWan() {

  // Get the tx count to determine next nonce
  return web3wan.eth.getTransactionCount(opts.to);

}
```


```js
function getBlockNumberEth() {

  // Get the current block number on Ethereum
  return web3eth.eth.getBlockNumber();

}

function getBlockNumberWan() {

  // Get the current block number on Wanchain
  return web3wan.eth.getBlockNumber();

}
```

```js
function printReceipt(receipt) {

  console.log('Receipt:', receipt);

}
```

Then let's define the remaining functions.

```js
function sendLock(txCount) {

  // Get the raw lock tx
  const lockTx = cctx.buildLockTx(opts);
  lockTx.nonce = web3eth.utils.toHex(txCount);

  // Sign and send the tx
  const transaction = new EthTx(lockTx);

  // sign tx with private key
  transaction.sign(ethPrivateKey);
  const serializedTx = transaction.serialize().toString('hex');

  // Send the lock transaction on Ethereum
  return web3eth.eth.sendSignedTransaction('0x' + serializedTx);
}

function confirmLock
```
