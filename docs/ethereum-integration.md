---
layout: page
title: Ethereum Integration
---

The Wanchain cross-chain feature currently includes mainnet and testnet
connections with the Ethereum network. This means that users can use the Wanchain
multi-party computing Storeman solution to convert ETH coin on Ethereum to wETH
(Wanchain ETH token) on the Wanchain network, as well as convert the Wanchain
token back to native ETH.

<!--
For a full overview of the cross-chain implementation, check out
[An Overview of the Wanchain Cross-Chain Implementation Model](https://medium.com/wanchain-foundation/an-overview-of-the-wanchain-2-0-cross-chain-implementation-model-c455cfd25664)
and the offical [Cross-Chain Implementation Reference](./). Useful for our
example cases that we will be working on, there is also
[documentation on the WanX](https://github.com/wanchain/wanx/blob/dev/) repo
for making inbound and outbound transactions on the various chains.

The short version of the story is that cross-chain transaction involve
(usually) four steps. For inbound transactions (converting native coin to the
corresponding token on Wanchain), the steps are as follows.
-->

For the Ethereum integration, the steps required for an inbound cross-chain
transaction are as follows:

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
the wETH token within 4 hours (the time until expiration for Ethereum), then
the transaction will move to a "Revoked" state. If that happens, the redeemer
will no longer be able to redeem the wBTC, and the sender will have to make a
`Revoke` call to get the locked ETH back.

The steps for outbound transactions are similar to the steps for inbound
transactions, though with a few minor differences. For Ethereum, the steps
are instead:

#### Steps for Outbound Ethereum Cross-chain Transaction
1. Make a transaction on Wanchain that locks the tokens and that includes the
   outbound fee in WAN.
2. Wait for a followup contract call on Ethereum from the Storeman group that
   confirms the lock.
3. Make a smart contract call on Ethereum to redeem the coin.
4. Wait for a followup contract call on Wanchain from the Storeman group that
   finalizes the transfer.

Besides working on opposite chains, the main difference from the steps for an
inbound transaction is that for an outbound transaction the `Lock` call to the
smart contract must include an outbound fee, priced in WAN.

Also, like with inbound transactions, if the outbound redeemer does not redeem within
the time limit then the transaction will go into a "Revoked" state and can no
longer be redeemed. In that case the `Revoke` call must be made by the sender to
get the locked wBTC back.

In the next pages, we will set up a connection to the Ethereum network, and
then start making cross-chain transactions with
[wanx](https://github.com/wandevs/wanx).
