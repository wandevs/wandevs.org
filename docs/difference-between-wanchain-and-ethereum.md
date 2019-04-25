---
layout: page
title: Differences between Wanchain and Ethereum
---

Since Wanchain started as a fork of Ethereum, it shares much of the same
structure and features as Ethereum. However, Wanchain adds two new key
features.

* Wanchain supports cross-chain transactions
* Wanchain supports privacy transactions

These features, and the need to make Wanchain distinct from Ethereum, required
a couple changes in the core stucture that developers need to take into
consideration when building Dapps.

### Txtype Field Added to Transaction

To facilitate privacy transactions a new field has been inserted in the transaction format called `Txtype`. This field can currently contain two values:

* 0x01 for normal transactions
* 0x06 for privacy transactions

This change, although quite small, has a big impact on libraries that create transations.

### Change in Address Checksum

The checksum of an Ethereum address was introduced by Vitalik in [EIP-55](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-55.md).
To minimize the risk of collisions between Ethereum and Wanchain addresses, the checksum was changed. This ensures that when using checksummed addresses the changes of accidentally using a Wanchain address on Ethereum and vice versa is minimized.


The checksum calculation for Ethereum looks like this:

```js
exports.toChecksumAddress = function (address) {
  address = exports.stripHexPrefix(address).toLowerCase()
  var hash = exports.sha3(address).toString('hex')
  var ret = '0x'

  for (var i = 0; i < address.length; i++) {
    if (parseInt(hash[i], 16) < 8) {
      ret += address[i].toUpperCase()
    } else {
      ret += address[i]
    }
  }

  return ret
}
```

The checksum calculation for Wanchain is changed to instead:

```js
exports.toChecksumAddress = function (address) {
  address = exports.stripHexPrefix(address).toLowerCase()
  const hash = exports.sha3(address).toString('hex')
  let ret = '0x'

  for (let i = 0; i < address.length; i++) {
    if (parseInt(hash[i], 16) >= 8) {
      ret += address[i].toUpperCase()
    } else {
      ret += address[i]
    }
  }

  return ret
}
```

So basically convert the address to hex, but if the ith digit is a letter (ie. it's one of abcdef) print it in lowercase if the 4\*ith bit of the hash of the lowercase hexadecimal address is 1 otherwise print it in uppercase.

### Change in Wallet Keyfile

The wallet file for Wanchain is different from an Ethereum wallet.

**Ethereum Wallet**
```js
{
  "version": 3,
  "id": "9a6ed622-daf7-45a2-977d-f938f9aca9af",
  "address": "2ee9e1513fec2344d4189c81b995734939e71ffc",
  "crypto": {
    "ciphertext": "0d903045538834aef35a597e1a1643c6aca89ebdf5ff78ae00f54865ca4b1837",
    "cipherparams": {
      "iv": "577abe28501c41d2f585e97e3e4b3313"
    },
    "cipher": "aes-128-ctr",
    "kdf": "scrypt",
    "kdfparams": {
      "dklen": 32,
      "salt": "8abca9a67c9006b26bfa894c505ec9568fc2fccc65628d405a061117ece8f1e3",
      "n": 131072,
      "r": 8,
      "p": 1
    },
    "mac": "6370c3eb5cf39992fb4b9b8077532d4fe2970afd1d58052c82dfe650815414de"
  }
}
```

**Wanchain Wallet**
```js
{
  "version": 3,
  "id": "817fdbdc-d48f-4bf9-af35-29afb1513dca",
  "address": "C01984f48888ec2026359A80D502890e501d4e28",
  "crypto": {
    "ciphertext": "3cb58b2beee11c91bd3f36da5f5efd72d719c539dc978be1791cd7731318a936",
    "cipherparams": {
      "iv": "2e3d7d475b5b80f566dddef6d761234c"
    },
    "cipher": "aes-128-ctr",
    "kdf": "scrypt",
    "kdfparams": {
      "dklen": 32,
      "salt": "99fc3d8833878ab602f32ebf811049d6cd204fb4e1bfdd5a57cccb3c651ad8ef",
      "n": 8192,
      "r": 8,
      "p": 1
    },
    "mac": "3d58fcd1cb5129e5db13e95a13fcbdbe5547105e49d90d9467a9fb35b2dacfd3"
  },
  "crypto2": {
    "ciphertext": "39318a4e976bab7942e084acbb3a56e50821535bb2826857b9b10bd700211ac8",
    "cipherparams": {
      "iv": "ec1831e036ab3a04674504b18bfa950e"
    },
    "cipher": "aes-128-ctr",
    "kdf": "scrypt",
    "kdfparams": {
      "dklen": 32,
      "salt": "c8e5f5b842c0af00c50f33b52818439e61c9fe5b7f254153c0279d9585a17d00",
      "n": 8192,
      "r": 8,
      "p": 1
    },
    "mac": "a6d504a6b73c33bc99a34041b7676ed070bc06cc600b2b3b8e076e6bf873cde3"
  },
  "waddress": "02404cc6c1b888b0050bb06683faf89325267d0e6b21d070a9cda363525fed30630263ed50506e25db5a4720a980288f10af1b4b346db585377ccc2d2c8c48f9cd2a"
}
```

As you can see above there is another object called `crypto2` as well as a `waddress` entry. The `waddress` is your private address and the `crypto2` object contains the second private key needed to create the one-time addresses associated with the privacy transactions. The wallet is backwards compatible with Ethereum though, so you could use a Wanchain wallet on both Ethereum and Wanchain.
