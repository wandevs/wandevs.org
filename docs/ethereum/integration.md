---
layout: page
title: Ethereum Integration
---

The Wanchain cross-chain feature currently includes mainnet and testnet
connections with the Ethereum network. This means that users can use the Wanchain
multi-party computing Storeman solution to convert ETH coin on Ethereum to WETH
(Wanchain ETH token) on the Wanchain network, as well as convert the Wanchain
token back to native ETH.

## Inbound Transactions

![Ethereum Inbound](/img/ethereum_inbound.png)

#### Steps for Inbound Ethereum Cross-chain Transaction

1. Make a transaction on Ethereum that locks funds with a particular smart
   contract.
2. Wait for a followup contract call on Wanchain from the Storeman group that
   confirms the lock.
3. Make a smart contract call on Wanchain to redeem the token.
4. Wait for a followup contract call on Ethereum from the Storeman group that
   finalizes the transfer.

Essentially there is a little ping-pong between the sender, the Storeman group,
and the receiver. The lock is initiated by the sender and then completed by
the Storeman group, and then the redeem is initiated by the receiver and then
completed by the Storeman group.

The smart contracts enforce that the funds are redeemed within a given time
period. That is, if the the funds are locked but the redeemer does not redeem
the token within 4 hours (the time until expiration for Ethereum), then the
transaction will move to a "Revoked" state. If that happens, the redeemer will
no longer be able to redeem the WETH, and the sender will have to make a
`Revoke` call to get the locked ETH back.

## Outbound Transactions

![Ethereum Outbound](/img/ethereum_outbound.png)

#### Steps for Outbound Ethereum Cross-chain Transaction

1. Make a transaction on Wanchain that locks the tokens and that includes the
   outbound fee in WAN.
2. Wait for a followup contract call on Ethereum from the Storeman group that
   confirms the lock.
3. Make a smart contract call on Ethereum to redeem the coin.
4. Wait for a followup contract call on Wanchain from the Storeman group that
   finalizes the transfer.

The steps for outbound transactions are similar to the steps for inbound
transactions, though with a couple small differences. Besides working on
opposite chains, the main difference from the steps for an inbound transaction
is that for an outbound transaction the `Lock` call to the smart contract must
include an outbound fee, priced in WAN.

Also, like with inbound transactions, if the outbound redeemer does not redeem
within the time limit then the transaction will go into a "Revoked" state and
can no longer be redeemed. In that case the `Revoke` call must be made by the
sender to get the locked WETH back.

## Connecting to Ethereum

To conduct cross-chain transactions from Ethereum into Wanchain, we will need
to be able to connect to the Ethereum network, both to send transactions and to
listen for contract events. The mainnet integration connects to Ethereum
mainnet, naturally, and the testnet integration connects with the Rinkeby
network.

Running an Ethereum comes with its own challenges, which we will not cover
here. While you can run your own Ethereum node, for the examples below we will
instead rely on Infura, a gateway service for the Ethereum network.

If you are running your own Ethereum, you can use web3 to connect to it by
passing in a new HTTP provider with the node's URL when initializing web3. For
example, if the node is running on the same machine as the script, we can
initialize web3 like so:

```js
const Web3 = require('web3');
const web3eth = new Web3(new Web3.providers.HttpProvider('http://localhost:18545');
```

To use Infura instead, we can initialize web3 in the same way, but instead of
passing in the URL of the Ethereum node, we pass in the Infura URL.

```js
const Web3 = require('web3');
const web3eth = new Web3(new Web3.providers.HttpProvider('https://rinkeby.infura.io/<myToken>');
```

Make sure to add your Infura token to the URL. For testnet you should use the
`rinkeby.infura.io` subdomain, as shown above, while for mainnet you should use
the standard `infura.io` domain.
