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
$ mkdir wanchain-ethereum-crosschain
$ cd !$
$ npm init
$ npm install --save wanx web3 keythereum ethereumjs-tx wanchainjs-tx
```

We'll use `wanx` to build cross-chain transactions, `web3` to submit the
transactions to the Wanchain and Ethereum networks, `keythereum` to access the
private keys, and `ethereumjs-tx` and `wanchainjs-tx` to build and sign
transactions before submitting them to the network.

To kick things off, create a new `utils.js` file, where we will put a couple of
helper functions to send transactions on Wanchain and Ethereum.

```bash
$ vi utils.js
```

#### utils.js
```js
const EthTx = require('ethereumjs-tx');
const WanTx = require('wanchainjs-tx');

module.exports = {
  sendRawEthTx,
  sendRawWanTx,
};

async function sendRawEthTx(web3, rawTx, fromAccount, privateKey) {

  // Get the tx count to determine next nonce
  const txCount = await web3.eth.getTransactionCount(fromAccount);

  // Add the nonce to tx
  rawTx.nonce = web3.utils.toHex(txCount);

  // Sign and serialize the tx
  const transaction = new EthTx(rawTx);
  transaction.sign(privateKey);
  const serializedTx = transaction.serialize().toString('hex');

  // Send the lock transaction on Ethereum
  const receipt = await web3.eth.sendSignedTransaction('0x' + serializedTx);

  return receipt;
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

These two functions allow us to send raw transactions by passing in the web3
object, the raw transaction object, the account address that the transaction is
to be sent from, and the sending account's private key.

Now, let's create a new node script named `eth2weth.js`. This will be just a
single file script that gets executed by calling `node eth2weth.js`. At the top
of the file, let's add our dependencies.

#### eth2weth.js
```js
const WanX = require('wanx');
const Web3 = require('web3');
const keythereum = require('keythereum');
const utils = require('./utils');
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

## Making the Transaction

At this point the script is sufficiently set up and we are now ready to make
the cross-chain transaction. To get this started, let's initialize a new
cross-chain transaction object.

```js
// New crosschain transaction
// ethereum, inbound
const cctx = wanx.newChain('eth', true);
```

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

Here we initialized `cctx` as an inbound (inbound = true) transaction on the
Ethereum (eth) chain. Next, let's go ahead and add in the basic logic of the
transaction. We'll fill in the missing functions in a bit.

```js
Promise.resolve([])
  .then(sendLock)
  .then(confirmLock)
  .then(sendRedeem)
  .then(confirmRedeem)
  .catch(err => {
    console.log('Error:', err);
  });
```

The full logic of the transaction is handled by a chain of promises that flow
through the required steps of the cross-chain transaction. The `lock` is sent
and then confirmed, and then the `redeem` is sent and finally confirmed.

Let's go ahead and define these 4 functions.

```js
async function sendLock() {

  // Get the raw lock tx
  const lockTx = cctx.buildLockTx(opts);

  // Send the lock tx on Ethereum
  const receipt = await utils.sendRawEthTx(web3eth, lockTx, opts.from, ethPrivateKey);

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

async function confirmRedeem() {

  // Get the current block number on Ethereum
  const blockNumber = await web3eth.eth.getBlockNumber();

  // Scan for the lock confirmation from the storeman
  const log = await cctx.listenRedeem(opts, blockNumber);

  console.log('Redeem confirmed:', log);
  console.log('COMPLETE!!!');
}

```

The `sendLock` and `sendRedeem` methods call `buildLockTx` or `buildRedeemTx`
to generate a new transaction object with the correct parameters. The
transaction objects are sent to our helpers functions, which get and attach the
`nonce` to the transaction object, signs the transaction with the private key,
and then finally serializes the transaction and sends it to the network.

The `listenLock` and `listenRedeem` methods are called to poll the network
for a transaction from the Storeman group that fits the transaction criteria.
If you don't want to rely on WanX to listen for those transactions, you can
alternatively use `buildLockScanOpts` and `buildRedeemScanOpts` to get the
parameters for the scan, and then make your own subscribe call to the network.

<div class="alert alert-info">
  <b>Note</b>: the <code>confirmRedeem</code> function call is technically not
  needed, since the Wanchain tokens are sent to the recipient's account once
  the <code>sendRedeem</code> succeeds. The example adds the final step only
  for completeness.
</div>

With all of these functions in place, we can now run our script.

```bash
$ node eth2weth.js
```

If everything goes well you should see some output and no printed errors. If
your script manages to send the lock transaction but fails to send the redeem
transaction, you can either try the redeem again by rerunning the script with
the redeemKey manually added, and with the initial lock transaction and listen
lock commented out. Otherwise, once the timelock expires you can revoke the
locked funds, as we will demonstrate in the next section.
