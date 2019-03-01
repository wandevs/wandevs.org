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
  chains.

For the case of Ethereum and ERC20 tokens, the cross-chain integration uses
smart contracts on both Wanchain and Ethereum. For Bitcoin, which does not
natively support smart contracts, the integration instead just relies on smart
contracts on Wanchain in combination with custom locking addresses on Bitcoin.

A cross-chain transaction essentially involves an interplay between the user
making the transaction and a Storeman group, who communicate and transfer value
by way of smart contracts. The exact sequence and usage of smart contracts is
dependent on which chain the cross-chain transaction is for.

## Process workflow

A cross-chain is divided into two basic parts: a `lock` and a `redeem`. The
locker and redeemer can be the same person or different people, though if they
are not the same person some information will have to be shared between the
two: in particular, the `x` value used in the initial lock request.

The `lock` involves a user sending funds to a particular smart contract or lock
address on either Wanchain or the original chain, depending on whether the
transaction is inbound or outbound from Wanchain.

The `redeem` involves a user calling a contract to have coin transferred to the
user's account, or the user spending the coins directly from the locking address.

Once a cross-chain transaction is initiated, the redeemer must recover the
coins or tokens before a certain period of time before the transaction expires.
The time until expiration is different for each chain (please see the following
sections of this documentation). If the redeemer fails to redeem locked coins
in time, the redeemer will not longer be able to recover the funds. Instead,
the cross-chain transaction becomes marked as expired, and the funds have to be
recovered by the sender.

### Inbound

When sending funds into Wanchain, the transaction is initiated when the sender
locks funds with the smart contract on the original chain. Or in the case of
Bitcoin, the sender creates and funds a locking Bitcoin address, and then calls
the contract on Wanchain to announce the locked funds.

If the lock request is successful, the Storeman group will detect the request
and react by calling the contract on Wanchain to set up the transaction and
allocate token. Once the Storeman group has called the Wanchain contract, the
redeemer can then call the Wanchain contract to collect the token.

If the redeemer fails to redeem before the lock expires, then the sender must
make a call to the smart contract on the original chain to recover the locked
funds.  Or in the case of bitcoin, the sender can just spend the coins from the
locking address.

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

And if the redeemer fails to redeem before the lock expires, the sender must
make a call to the smart contract on Wanchain to recover the locked token.
