---
layout: page
author: tyrion70
title: Using the development network
---
You can start a simple development wanchain node with the --dev option:
```js
gwan --dev --datadir ./devTest/ --etherbase 0x9da26fc2e1d6ad9fdd46138906b0104ae68a65d8 --unlock 0x9da26fc2e1d6ad9fdd46138906b0104ae68a65d8,0x2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e --password ./pwdfile --mine --minerthreads 1 --rpc --rpcaddr 0.0.0.0 --rpcapi eth,personal,net,admin,wan,txpool
```

The keystores need to be placed in the ./devTest/keystore directory and are located in:
```js
./DOCKER/data/keystore/UTC--2016-09-17T07-06-12.178151982Z--9da26fc2e1d6ad9fdd46138906b0104ae68a65d8
./DOCKER/data/keystore/UTC--2017-05-14T03-13-33.929385593Z--2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e
```

The address ```0x9da26fc2e1d6ad9fdd46138906b0104ae68a65d8``` is allowed to mine because of these lines of code in the ```core/genesis.go``` file:

```js
func DevGenesisBlock() *Genesis {
        return &Genesis{
                Config:     params.AllProtocolChanges,
                Nonce:      42,
                ExtraData:  hexutil.MustDecode("0x9da26fc2e1d6ad9fdd46138906b0104ae68a65d8"),
                GasLimit:   4712388,
                Difficulty: big.NewInt(1),
                Alloc:      jsonPrealloc(wanchainPPOWDevAllocJson),
        }
}
```

The allocation of funds goes to these addresses as described in the ```core/genesis_alloc.go``` file:
```js
const wanchainPPOWDevAllocJson = `{
          "0x2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e": {"balance": "1000000000000000000000"},
          "0x8b179c2b542f47bb2fb2dc40a3cf648aaae1df16": {"balance": "1000000000000000000000"},
          "0x7a22d4e2dc5c135c4e8a7554157446e206835b05": {"balance": "3000000000000000000000000000"}
}`
```
