---
layout: page
author: jgoyette
title: Crosschain Overview
---

Wanchain's cross-chain feature allows users to convert a native coin or token,
such as Bitcoin or the DAI token on Ethereum, into the corresponding token on
the Wanchain network, as well as convert from the Wanchain token back to the
native coin or token.

## Infrastructure

There are two main components of the Wanchain infrastructure that enable the
cross-chain feature.

- **Storeman group**, which is a group of mediator nodes that handle and fund
  the cross-chain transaction.
- **Smart contracts**, which are deployed on Wanchain as well as the native
  chain.

For the case of Ethereum and ERC20 tokens, the cross-chain integration uses
smart contracts on both Wanchain and Ethereum. For Bitcoin, which does not
natively support smart contracts, the integration instead just relies on smart
contracts on Wanchain in combination with custom locking addresses on Bitcoin.

A cross-chain transaction essentially involves an interplay between the user
making the transaction and a Storeman group, who communicate and transfer value
by way of smart contracts. The exact sequence and usage of smart contracts is
dependent on which chain the cross-chain transaction is for.

## Process workflow

A cross-chain is divided into two basic parts: a lock and a redeem. The locker
and redeemer can be the same person or different people, though if they are not
the same person some information will have to be shared between the two: in
particular, the `x` value used in the initial lock request.

### Inbound

When sending funds into Wanchain, the transaction is initiated when the sender
locks funds with the smart contract on the original chain. In the case of
Bitcoin, the sender creates and funds a locking Bitcoin address, and then calls
the contract on Wanchain to announce the locked funds.

If the lock request is successful, the
Storeman group will detect the request and react by calling the contract on
Wanchain to set up the transaction and allocate token. Once the Storeman group
has called the Wanchain contract, the redeemer can then call the Wanchain
contract to collect the token.


### Inbound Cross-Chain Workflow for Ethereum

![Ethereum Inbound](/img/ethereum_inbound.png)

1. User sends lock request on Ethereum
2. Storeman group sends lock confirmation on Wanchain
3. User sends redeem request on Wanchain
4. Storeman group sends redeem confirmation on Ethereum

### Outbound

When sending funds out of Wanchain, the transaction is initiated when the
sender locks the Wanchain token in the Wanchain contract. If the lock request
is successful, the Storeman group will detect the request and react by calling
the contract on the original chain to set up the transaction. In the case of
Bitcoin, the Storeman group creates a new lock address and allocates funds by
sending the requested amount to the locking address.

Once the Storeman group has reacted, the redeemer can then call the contract on
the original chain to collect the coin or token. Or in the case of Bitcoin, the
redeemer can just spend the coins from the locking address.
