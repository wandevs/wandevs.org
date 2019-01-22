---
layout: post
author: jsgoyette
title: How to set up a Bitcoin node for Wanchain Development
permalink: /faqs/how-to-setup-bitcoin-node.html
---

These instructions cover how to set up a bitcoind node with RPC enabled, so
that the node can be accessed programmatically.

## Install bitcoind

There are more ways than what is listed here to obtain the bitcoind binary.
Below are a couple of the easier ways.

#### Option 1 - Install Bitcoin Core

On Mac, installing the Bitcoin Core package is the simplest way to get the bitcoind binary.

- Download and install Bitcoin Core ([https://bitcoin.org/en/choose-your-wallet](https://bitcoin.org/en/choose-your-wallet))

#### Option 2 - Install bitcoind from ppa

On Ubuntu, you can install bitcoind easily from the ppa.

```bash
$ sudo apt-add-repository ppa:bitcoin/bitcoin
$ sudo apt-get update
$ sudo apt-get install bitcoind
```
## Configure node settings

Update the bitcoin config file (`~/.bitcoin/bitcoin.conf`) and add the required
settings to allow RPC connections to the node. Use at least the following
config settings.

```
server=1
rpcuser=myuser
rpcpassword=mypassword
txindex=1
```

If you want to be able to connect to the bitcoin node from another machine,
also set the `rpcallowip` option. Though if you decide to open up the node to
the outside, make sure you protect it somehow. You do not want anybody but
yourself to be able to RPC connect to the node!

```
rpcallowip=192.168.0.0/24
```

## Start bitcoind

Start the bitcoind daemon in testnet mode.

```bash
$ bitcoind -testnet -daemon
```

## Check connection

Test that you can connect to the Bitcoin node from the command line using
`bitcoin-cli`.

```bash
$ bitcoin-cli -testnet getbestblockhash
```
