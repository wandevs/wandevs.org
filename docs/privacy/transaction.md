---
layout: page
author: jgoyette
title: Make a Privacy Transaction
---

Privacy transactions on Wanchain require a two-step process, where WAN coin is
first locked in the built-in privacy contract, and then is later redeemed from
the contract. Generally, the redeem should not be made right after the lock is
made, otherwise the obscurity provided by the privacy mechanism is lost, in
that an outsider would be able to associate the redeem to the particular lock.
For this reason, the following examples have the two steps divided into
separate scripts.

### Set up

The following code example sends WAN to another account, and thus to run the
examples on your own you will need to have two separate Wanchain accounts, both
with a positive WAN balance so as to be able to pay for the gas for contract
calls.

If you do not already have a second Wanchain account, go ahead and create
one now. You can use the same process that you used to create the first
account, or any of the methods mentioned
[here](/docs/create-wanchain-account/). After creating the second account, go
ahead and send a small amount of WAN to the new account. About 1 WAN should
suffice.

With the two accounts in place, create a new directory where we'll add the
example scripts.

```bash
$ mkdir wanchain-privacy
$ cd !$
```

Now go ahead and create a new file and add the privacy contract ABI and
address.


```bash
$ vi contract.js
```

**contract.js**
```js
const privacyABI = [{"constant":false,"type":"function","stateMutability":"nonpayable","inputs":[{"name":"OtaAddr","type":"string"},{"name":"Value","type":"uint256"}],"name":"buyCoinNote","outputs":[{"name":"OtaAddr","type":"string"},{"name":"Value","type":"uint256"}]},{"constant":false,"type":"function","inputs":[{"name":"RingSignedData","type":"string"},{"name":"Value","type":"uint256"}],"name":"refundCoin","outputs":[{"name":"RingSignedData","type":"string"},{"name":"Value","type":"uint256"}]},{"constant":false,"inputs":[],"name":"getCoins","outputs":[{"name":"Value","type":"uint256"}]}];

const privacyAddress = '0x0000000000000000000000000000000000000064';

module.exports = {
  privacyABI,
  privacyAddress,
};
```

### Lock the coin

Create a new file called `lock.js`, which will be the script that locks coins
in the privacy contract, and add code to initialize a new `wan3` object and
privacy contract object, as well as the sender and recipient addresses, and the
amount to be sent (in Wan).

```bash
$ vi lock.js
```

**lock.js**
```js

const Wan3 = require('wan3');
const {
  privacyABI,
  privacyAddress,
} = require('./contract.js');

const wan3 = new Wan3(new Wan3.providers.HttpProvider('http://localhost:8545'));
const privacyContract = new wan3.eth.Contract(privacyABI, privacyAddress);

const sender = '0xa6d72746a4bb19f46c99bf19b6592828435540b0';
const recipient = '0xb1e2de3ca64046eea0d0d2bdd7aff1355838b9b4';

const amount = wan3.utils.toWei('10');
```

Note that the amount should be one of the allowed values listed in the
[overview of privacy transactions](/docs/privacy/overview/).

Also notice that we are using `wan3` instead of `web3`. We will need to call
certain Wanchain endpoints, and those endpoints are not registered in `web3`.

Now let's set up the function that will lock the coin, which we'll define as
`lockCoin`.

```js
Promise.resolve([])
  .then(lockCoin)
  .catch(err => {
    console.log('Error:', err);
  });

async function lockCoin() {

  // generate OTA from recipient account
  const wanAddr = await wan3.wan.getWanAddress(recipient);
  const ota = await wan3.wan.generateOneTimeAddress(wanAddr);

  // generate transaction data for contract call
  const buyData = privacyContract.methods.buyCoinNote(ota, amount).encodeABI();

  console.log('wanAddr:', wanAddr);
  console.log('ota:', ota);
  console.log('buyData:', buyData);

  // submit transaction
  const buyResult = await wan3.eth.sendTransaction({
    from: sender,
    to: privacyAddress,
    value: amount,
    data: buyData,
    gas: 1000000,
  });

  console.log('buyResult:', buyResult);

  return ota;
}
```

The `lockCoin` function gets the `WanAddress` for the recipient address, and
then uses that to generate a one-time address (OTA). Then, with the OTA, it
constructs the transaction data for the call to the privacy contract, and then
submits the transaction.

<div class="alert alert-info">
  <b>Make sure to hang on the OTA, as it is needed to redeem the coin.</b> If the
  recipient account is owned by another person, instead of generating the OTA
  yourself from the recipient address, you should request that the recipient
  provide you with an OTA. Otherwise, if you generate the OTA, you will need to
  pass along the generated OTA to the recipient after you lock the coin so that
  the coin eventually can be redeemed.
</div>

With all that in place you should now be able to run the script.

```bash
$ node lock.js
```

### Redeem the coin

Now that we have some coins locked, let's create another script to redeem the
coins. Start by creating a new file called `redeem.js`.

```bash
$ vi redeem.js
```

The redeem script will have the same variables defined as the lock script, so
go ahead and copy over those variables. The only thing we need to add is the
OTA that was generated by the lock script.

```js
const ota = '0x0207f3bb30e812f04d7f1bb98623471aaafc111...';
```

With that added, let's add the function to make the redeem call on the privacy
contract.

```js
Promise.resolve([])
  .then(redeemCoin)
  .catch(err => {
    console.log('Error:', err);
  });

async function redeemCoin() {

  // generate a new mixset from the OTA (5 indicates the number of keys to mix with)
  const mixWanAddresses = await web3.wan.getOTAMixSet(ota, 5);

  // get the OTA private key
  const keyPairs = await web3.wan.computeOTAPPKeys(recipient, ota);
  const privateKey = keyPairs.split('+')[0];

  // generate transaction data for contract call
  const ringSignData = await web3.wan.genRingSignData(recipient, privateKey, mixWanAddresses.join("+"));
  const refundData = privacyContract.methods.refundCoin(ringSignData, amount).encodeABI();

  const refundResult = await web3.eth.sendTransaction({
    from: recipient,
    to: privacyAddress,
    value: 0,
    data: refundData,
    gas: 2000000,
  });

  console.log('refundResult:', refundResult);
}
```

The `redeemCoin` function generates a new mixset from the given OTA with the
given number of keys, and then generates the ring signature data from the
mixset and the private key of the OTA. Finally, it constucts the transaction
data for the contract call and submits the transaction to the network.

Now, go ahead and run the script.

```bash
$ node redeem.js
```
