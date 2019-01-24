---
layout: page
title: How to set up a Wanchain node for Development
---

This article covers how to set up a Wanchain node with RPC enabled, so
that the node can be accessed programatically or by the console.

The gwan node that runs when the WanWallet GUI wallet is open does not have RPC
enabled, and thus it is not sufficient to just open up the WanWallet and use
its gwan node. When the WanWallet is closed, you can, however, use the gwan
binary shipped with the wallet to start a node with RPC enabled. Otherwise, if
you do not have WanWallet installed, you will need to download or build the
gwan binary.

Overall, the steps below include getting gwan, running gwan with the correct
options, and testing that you can connect to the node via RPC by starting the
console.

## Install and run gwan

#### Option 1 - Wallet

Install the WanWallet GUI wallet and start a testnet Wanchain node using the
gwan binary that ships with the wallet.

- Download and install the Wanchain wallet [https://wanchain.org/products](https://wanchain.org/products)
- Start the node in testnet mode

```bash
$ ~/.config/WanWalletGui/binaries/Gwan/unpacked/gwan --testnet \
	--verbosity 4 --maxpeers 500 --maxpendpeers 100 --gasprice 180000000000 --txpool.nolocals \
	--rpc --rpcaddr 0.0.0.0 --rpcport 18545 --rpcapi "debug,eth,personal,net,admin,wan,txpool"
```


#### Option 2 - Pre-built gwan binary

Download a pre-built binary and use it to start a testnet Wanchain node.

- Download the binary [https://wanchain.org/products](https://wanchain.org/products)

```bash
$ wget https://wanchain.org/download/gwan-linux-amd64-1.0.7-3c1c638c.tar.gz
$ tar xzvf gwan-linux-amd64-1.0.7-3c1c638c.tar.gz
$ cd gwan-linux-amd64-1.0.7-3c1c638c
```
- Start the node in testnet mode

```bash
$ ./gwan --testnet \
	--verbosity 4 --maxpeers 500 --maxpendpeers 100 --gasprice 180000000000 --txpool.nolocals \
	--rpc --rpcaddr 0.0.0.0 --rpcport 18545 --rpcapi "debug,eth,personal,net,admin,wan,txpool"
```

#### Option 3 - Build from source

Download the go-wanchain source code and build the gwan binary from source. Use
the built binary to start a testnet Wanchain node.

- Install and configure Golang [https://golang.org/doc/install](https://golang.org/doc/install)
- Get and build go-wanchain

```bash
$ go get github.com/wanchain/go-wanchain
$ cd $GOPATH/src/github.com/wanchain/go-wanchain
$ make gwan
```
- Start the node in testnet mode

```bash
$ ./build/bin/gwan --testnet \
	--verbosity 4 --maxpeers 500 --maxpendpeers 100 --gasprice 180000000000 --txpool.nolocals \
	--rpc --rpcaddr 0.0.0.0 --rpcport 18545 --rpcapi "debug,eth,personal,net,admin,wan,txpool"
```

## Test the connection

Check to see that you can connect to the Wanchain node from the command line by
starting a console.

```bash
# Replace the path to gwan with the path that you used when starting the node
$ ./gwan attach http://localhost:18545
```
