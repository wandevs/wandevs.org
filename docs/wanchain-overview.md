---
layout: page
title: Overview
---


## Mission and Vision

Bitcoin was launched in 2009 by an anonymous individual named Satoshi Nakamoto. In his whitepaper Satoshi outlined his vision for "A peer to peer electronic cash system". As time went by, Bitcoin started getting traction and was increasingly used in commerce.
Ethereum was launched in 2015 with the intent of creating a network for "programmable money" which could one day become the backbone of a new decentralized financial system.

Time went by, and many new blockchains started to appear each one for a different use case privacy, scalability, governance... These protocols all had a different vision, roadmap and community.
It became obvious that the future of this industry would depend on many heterogenous blockchains which would each have its own set of rules and governance processes.

These blockchains would each be in their own bubble, siloed and closed to the outside.
Much like the days of the intranet, when we had private networks which could not communicate between one and another. The question now arose, how do we go from  a network of intranets to the internet?

How do we transition from an  ecosystem of siloed blockchains to the internet of blockchains?
Wanchain is a project that aims to answer this question. It  started in 2016 as an effort to build an infrastructure which could support the transfer of value between different blockchains.

### Vision

The rise of Bitcoin and Ethereum was the first step toward creating a new global financial system. Where people are able to transact, enter into agreements and financial settlements in a peer to peer manner without requiring a third party to instigate trust in the process.
The current industry however, is far from being fully decentralized. Many assets are entrusted to centralized institutions such as exchanges. This stems from the fact that heterogenous chains cannot communicate with one and another, so whenever users needs to exchange between different assets they have to rely on exchanges to make the transaction.

This current situation has become a threat for the cryptocurrency ecosystem as a monopoly has been formed by those exchanges. Large sums of funds are trusted to these entities which could at any moment get hacked, compromised by malevolent actors etc...
Any monopoly can be dangerous for the future of an industry, for such a young space it could alter innovation and delay the coming of a global decentralized financial system.

For this very reason, Wanchain believes the problem of interoperability needs to be solved in order for the cryptocurrency industry to achieve its true potential.
Our vision is to build out a blockchain which allows for decentralized cross chain transfer of value between heterogenous chains. Wanchain would be the platform which to host financial dApps that need to access different kind of assets

## Quick Technical Overview

Wanchain is its own chain, established as a fork from the Ethereum project, and it has its own genesis block and thus shares no history with Ethereum and has an entirely distinct blockchain from the Ethereum blockchain. Upon forking the Ethereum project, a few additions and changes were made to the protocol, which are outlined in detail in the next section that covers the differences between Wanchain and Ethereum.

Being a fork of Ethereum, Wanchain provides much of the same framework, such as an EVM that runs Solidity-based contract code. As a consequence, practically all the project builts on Ethereum could be deployed to Wanchain with no or only minimal changes, and utility packages built for Ethereum (like Truffle, Web3, etc.) can be used on Wanchain as well.

In addition to the feature set inherited from Ethereum, Wanchain also adds two new features: privacy transactions, and cross-chain transactions.

#### Privacy Transactions

Wanchain provides a privacy smart contract built into the protocol that uses ring signatures to mask the intended recipient of a transaction. The contract allows users to "lock" a particular amount of WAN into the contract, which then can later be retrieved by the recipient.

#### Cross-chain Transactions

Wanchain also provides a mechanism to convert digital assets on specific chains (currently Bitcoin, Ethereum and selecct ERC20 tokens) to representative tokens on the Wanchain network. For each asset that is integrated into Wanchain, there is a corresponding WRC20 token on Wanchain that can be traded and transacted, and eventually converted back at a 1-to-1 rate with the original digital asset. The conversions of the assets to tokens (and vice versa), also known as cross-chain transactions, are handled by Storeman group nodes, which rely on multi-party computing to ensure that funds remain safe and that bad actors cannot steal funds.
