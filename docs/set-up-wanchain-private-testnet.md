---
layout: page
title: How to setup a Wanchain private network
---
This article covers the step to make a private testnet preconfigured on chainid 99. 
You cannot run a testnet on the mainnet id (1) or testnetid (3)
without changing a lot of code yourself. 

There are two allowed signers configured to work for chain 99. These are:

  * 9da26fc2e1d6ad9fdd46138906b0104ae68a65d8
  * 2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e
  
The password of these keystores is password

## How to start the testnet
If you just want to run a testnet without knowing how it works, use the following information. 

The testnet can run on a single node. To run it start: ```dev_run_testnet.sh```
This will initialize a new testnet in the current directory and start gwan. 
To add a second miner use the ```dev_run_testnet2.sh``` file 
and to start a node that doesn't mine use ```dev_run_testnet3.sh```. 
These last two require the first to be running.

## How does it work
If you want to know how the various parts actually work together read on. 

The first script ```dev_run_testnet.sh``` does a number of things. 
We'll walk you through the steps below.

```
DATADIR=./data_testnet
ACCOUNT=0x2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e
KEYSTORE=UTC--2017-05-14T03-13-33.929385593Z--2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e
echo "run geth in testnet"
# Cleanup
rm -rf $DATADIR
mkdir -p $DATADIR/keystore
echo "wanglu" > ./passwd.txt
cp $KEYSTORE $DATADIR/keystore
#./gwan --datadir $DATADIR init genesis_testnet.json
#networkid='--testnet'
networkid='--networkid 99'
```
The above part does nothing more than set a few variables and does some cleanup. 
Every time you start this script it will throw away the complete blockchain. 
If you don't want this hash out the ```rm -fr``` line``
