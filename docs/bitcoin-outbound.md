---
layout: page
title: Bitcoin Outbound Transaction
---

Now that we've successfully done an inbound Bitcoin cross-chain transaction,
we can now do an outbound transaction.

<div class="alert alert-info">
  <b>Note</b>: Outbound transactions require that the sending Wanchain account
  has a positive balance of the cross-chain token. To convert back to Bitcoin,
  for example, your Wanchain account needs have some WBTC token.
</div>

Start by creating a new script file.

```bash
$ vi wbtc2btc.js
```

The set up portion of the script will be mostly identical to the one in the
`btc2wbtc.js` script covered above.

#### wbtc2btc.js
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

For outbound transactions there are a couple of additional details that we need
to add. First, in the `opts` object, the `to` now should be the receiving
Bitcoin address, and the `from` should be the sending Wanchain account. The
`wanKeyObject` should be derived from the `from` account. Also, since to redeem
you will need to spend the Bitcoin that are placed into the P2SH lock address,
the `opts` should include the WIF of the private key for the redeeming Bitcoin
address.

<div class="alert alert-info">
  <b>Note</b>: If you do not want to pass the private key, you can also
  construct the redeem transaction by using `wanx` to get the
  `hashForSignature`, then signing it on your own, and then submitting to
  `wanx` the signed hash (sigHash) and the public key of the redeeming address.
</div>

The redeeming Bitcoin address actually does not need to have any funds on it or
sent to it in the cross-chain transaction. Think of the redeeming Bitcoin
address as just a key for the lock address. When you redeem you can choose to
have the funds sent to the redeeming address, or instead have them sent to some
other address, even if the other address is a P2SH or Bech32 segwit address.

Let's get started by creating a new Bitcoin address that will be the redeemer.
Along with the address, we'll also need to get the private key for the address.
Remember, this address must be a legacy address.

```bash
$ redeemer=$(bitcoin-cli -testnet getnewaddress '' legacy)
$ echo $redeemer
$ bitcoin-cli -testnet dumpprivkey $redeemer
```

While we are at it, let's create another address where we'll have the redeemed
funds sent.

```bash
$ payTo=$(bitcoin-cli -testnet getnewaddress)
$ echo $payTo
```

Now add the redeemer address, the redeemer WIF, and the receiving address to
our `btc2wbtc.js` script.

```js
const redeemerAddress = 'mvTfNujpcQwHaefMxfJRix4vhfNBxSFbBe';
const redeemerWIF = 'cNggJXP2mMNSHA1r9CRd1sv65uykZNyeH8wkH3ZPZVUL1nLfTxRM';
const payToAddress = '2MtfSLeCdCN4rzX6uwdZRSr25Sa1esKe424';
```

At last, let's define the transaction options.

```js
const opts = {
  // Sending wanchain address
  from: '0xa6d72746a4bb19f46c99bf19b6592828435540b0',

  // Redeemer Bitcoin address
  to: redeemerAddress,
  wif: redeemerWIF,

  // Receiving Bitcoin address
  payTo: payToAddress,

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

We'll also want to define the amount of mining fee we want to pay when we
redeem the bitcoin. Let's do that now.

```js
// Total miner fee for revoke (in satoshis)
const minerFee = '600';
```

Next we will initialize WanX, though now as an outbound transaction.

```js
// New crosschain transaction
// bitcoin, outbound
const cctx = wanx.newChain('btc', false);
```

And again, we will log out the `opts`, in case we need to know and use the
generated `redeemKey`.

```js
console.log('Tx opts:', opts)
```

Now we are ready to kick off the transaction. The format of an outbound Bitcoin
transaction is similar to an outbound Ethereum transaction, except that the
redeem is accomplished by just spending the bitcoin out of the lock address.

Let's add in the chain of promises that will form the outbound transaction.

```js
Promise.resolve([])
  .then(sendLock)
  .then(confirmLock)
  .then(redeemBitcoin)
  .catch(err => {
    console.log('Error:', err);
  });
```

And let's go ahead and define these functions.

```js
async function sendLock() {

  console.log('Starting btc outbound lock', opts);

  // Get the outbound fee
  const fee = await cctx.getOutboundFee(opts);

  // Attach outboundFee to opts
  opts.outboundFee = fee;

  // Get the raw lock tx
  const lockTx = cctx.buildLockTx(opts);

  // Send the lock transaction on Wanchain
  const receipt = await utils.sendRawWanTx(web3wan, lockTx, opts.from, wanPrivateKey);

  console.log('Lock submitted and now pending on storeman');
  console.log(receipt);

  return receipt;
}

async function confirmLock(receipt) {

  // Scan for the lock confirmation from the storeman
  const res = await cctx.listenLock(opts, receipt.blockNumber);
  const { log, inputs } = res;

  console.log('Lock confirmed by storeman');
  console.log(log, inputs);

  // Add lockTime and txid to opts
  opts.lockTime = Number(inputs.lockedTimestamp);
  opts.txid = inputs.txHash;
}

async function redeemBitcoin() {

  // Build the contract to get the redeemScript
  const contract = cctx.buildHashTimeLockContract(opts);

  console.log('P2SH contract', contract);

  opts.redeemScript = contract.redeemScript;

  // Subtract miner fee
  const redeemValue = (new BigNumber(opts.value)).minus(minerFee).toString();

  // Get signed redeem tx
  const signedTx = cctx.buildRedeemTxFromWif(
    Object.assign({}, opts, {
      value: redeemValue,
    }),
  );

  console.log('Signed redeem tx:', signedTx);

  // Send the redeem tx to the network
  const txid = await utils.sendRawBtcTx(bitcoinRpc, signedTx);

  console.log('Redeem sent to network');
  console.log('TXID:', txid);
  console.log('COMPLETE!!!');
}
```

Like with outbound Ethereum transactions, the `sendLock` function includes an
additional step to get the outbound fee and to attach it to the `opts` object
before passing the `opts` into the `buildLockTx` method. Once the lock
transaction is sent, it then waits for a confirmation response from the
Storeman group. Once the response is received it grabs the reported txid and
lockTime and adds the values to `opts`, and then builds the redeem Bitcoin
transaction and sends it to the network.

That's it. Go ahead and run the script.

```bash
$ node wbtc2btc.js
```

If any errors occur, it is likely that it is with the redeeming Bitcoin
transaction. Make sure that you correctly set the WIF in the transaction
`opts`. If you need to resubmit the signed transaction to the network, you can
do so with:

```bash
$ bitcoin-cli -testnet sendrawtransaction 0100000001a3065af9cb47d7563a9f7fac4630581e5a4a48ad8af568b20aa73b44d7668d4000000000ec4830450221009cfc8a8cb5915a7a4f187e57ef00233ad8c682508e5d0d83ca18c128443c98c8022031126dc1443aaf767d40f73544712cda861b3008c2997ebc41fe63fd5c325c9c01210221cc1887d1c0d68b6f1d87a5d31504c74212c47ca9b20f462db06131cf0e67d52009f2b674484dec52f9dacae6f127ad63d18bb0d936ae717574ff62072e815a15514c5d63a8200a5ea11757ec3ec243f564cc15cdc511cd21b36e3e2e787ee8e0606aed8eee0d8876a914a6abc813d1ee8b9d784c521842225903a4884daf6704c84c655cb17576a91483e5ca256c9ffd0ae019f98e4371e67ef5026d2d6888acffffffff01a0860100000000001976a914a6abc813d1ee8b9d784c521842225903a4884daf88ac00000000
```
