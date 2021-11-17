---
layout: page
title: Set up a Wanchain Node (Deprecated)
---

To be able to access the Wanchain network from your program, you need to have
access to a running Wanchain gwan node with RPC enabled. The overview below
covers a few of the ways to get gwan, plus how to start gwan with the required
options, as well as how to check that you are able to make an RPC connection to
the node.

## Install gwan

There are a few ways to get the `gwan` binary. Either you can use the binary
from the wallet, download the binary, or build the binary yourself.

#### Option 1 - Use WanWallet gwan

The gwan node that runs when the WanWallet GUI wallet is open does not have RPC
enabled, and thus it is not sufficient to just open up the WanWallet and use
its running gwan node. But if you already have WanWallet installed, you can use
the gwan binary shipped with the wallet to start a node with RPC enabled.

- Download and install the Wanchain wallet: [https://wanchain.org/products](https://wanchain.org/products)

Once WanWallet is installed, the gwan binary should now exist on your system.

```bash
$ ls ~/.config/WanWalletGui/binaries/Gwan/unpacked/gwan
```

Either use that path in place of `gwan` in future commands, or add it to somewhere in your `PATH`.


#### Option 2 - Pre-built gwan binary

If you do not already have the WanWallet installed, the easiest way to get the
Wanchain node binary is to download the latest official release binary from the
Wanchain Foundation website.

- Download the binary: [https://wanchain.org/products](https://wanchain.org/products)

```bash
$ wget https://wanchain.org/download/gwan-linux-amd64-1.0.7-3c1c638c.tar.gz
$ tar xzvf gwan-linux-amd64-1.0.7-3c1c638c.tar.gz
$ cd gwan-linux-amd64-1.0.7-3c1c638c
```

In that directory you should find the `gwan` binary. You can either install the binary
to somewhere in your `PATH`, or you can run the binary directly with `./gwan`.

#### Option 3 - Build from source

There may be a reason why you do not want to use the official release binary,
and instead need to build gwan yourself. You can build from source with the
following steps.

- Install and configure Golang: [https://golang.org/doc/install](https://golang.org/doc/install)
- Get and build go-wanchain

```bash
$ go get github.com/wanchain/go-wanchain
$ cd $GOPATH/src/github.com/wanchain/go-wanchain
$ make gwan
```

The `gwan` binary should now exist in the `./build/bin/` directory. You can
either install the binary to somewhere in your `PATH`, or you can run the
binary directly with `./build/bin/gwan`.

## Start development node

With `gwan` installed on your system, you can now use it to start a development
Wanchain node in testnet mode. Running the node with the following options
should cover most of your needs.

```bash
$ gwan --testnet \
	--verbosity 4 --maxpeers 500 --maxpendpeers 100 --gasprice 180000000000 \
	--rpc --rpcaddr 0.0.0.0 --rpcport 18545 --rpcapi "debug,eth,personal,net,admin,wan,txpool"
```

But if not, you can peruse the various gwan options using the `help` command.

```bash
$ gwan help
```

## Test the connection

Now that gwan is installed and running, check to see that you can connect to
the Wanchain node from the command line by starting a new console.

```bash
# Replace the path to gwan with the path that you used when starting the node
$ gwan attach http://localhost:18545
```

## Run mainnet node

Once you have used the test network enough, you may want to start a mainnet
node. To do that, remove the `--testnet` option and run the RPC server instead
on port 8545. You may also want to remove the `-rpcaddr` option, or change it
to a local address, like `192.168.0.0/24`.

```bash
$ gwan \
	--verbosity 4 --maxpeers 500 --maxpendpeers 100 --gasprice 180000000000 \
	--rpc --rpcaddr 0.0.0.0 --rpcport 8545 --rpcapi "debug,eth,personal,net,admin,wan,txpool"
```
