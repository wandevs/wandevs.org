---
layout: page
author: tyrion70
title: How to setup a Wanchain private network
---
This article covers the step to use or make a private testnet preconfigured on chainid 99.
You cannot run a testnet on the mainnet id (1) or testnetid (3)
without changing a lot of code yourself. 

There are two allowed signers configured to work for chain 99. These are:

  * 9da26fc2e1d6ad9fdd46138906b0104ae68a65d8
  * 2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e
  
The password of these keystores is password1

## How to start the private testnet
If you just want to run a private testnet without knowing how it works, use the following information. 

The private testnet can run on a single node. To run it start: ```dev_run_privatenet.sh```
This will initialize a new private testnet in the current directory and start gwan. 
To add a second miner use the ```dev_run_privatenet2.sh``` file 
and to start a node that doesn't mine use ```dev_run_privatenet_no_mining.sh```. 
These last two require the first to be running.

## How does it work
If you want to know how the various parts actually work together read on. 

In order to create a private network a few things need to be arranged. To run a network you need to mine blocks. Mining in Wanchain is done with PPOW. In short you tell beforehand which addresses are allowed to sign new blocks. Then you start mining with a node that has that address in its wallet (unlocked).

### PPOW signers
The signers are located in this file ```core/genesis_ppw_signers.go```.  Changing the signers creates a new genesis block, so it requires you to change the hash of the genesis block in ```params/config.go``` as well.

### Genesis json
The genesis.json file ```core/genesis_privatenet.json```contains the default genesis information. This information however is overwritten/merged in the ```core/genesis.go``` file

```
func DefaultPrivateGenesisBlock() *Genesis {
	fmt.Print("DefaultPrivateGenesisBlock");
        return &Genesis{
                Config:     params.PrivateChainConfig,
                Nonce:      99, //same with the version
                ExtraData:  hexutil.MustDecode(getPrivatePpwSignStr()),
                GasLimit:   0x2fefd8,
                Difficulty: big.NewInt(1),
                Alloc:      jsonPrealloc(wanchainPrivateAllocJson),
        }
}
```

The allocation of funds is arranged in the ```core/genesis_alloc.go``` file. 

### Bootnodes (optional)
If you want to run multiple nodes, its important to tell them to find eachother. To do this you start the node with a nodekey (ie ```--nodekey ./bootnode/privatenet1```). This gives the node a specific ID you can then reference in the second node with the 

```--bootnodes "enode://9c6d6f351a3ede10ed994f7f6b754b391745bba7677b74063ff1c58597ad52095df8e95f736d42033eee568dfa94c5a7689a9b83cc33bf919ff6763ae7f46f8d@127.0.0.1:17718"``` 

option, where you put the ID of the first node together with the IP and port. 

### Putting it all together and starting a node
Once you made all the necessary changes, build gwan and start your node with:
```
./build/bin/gwan ${NETWORKID} --etherbase "${ACCOUNT1}" --nat none --gasprice '200000' --verbosity 4 --datadir $DATADIR  \
    --unlock "${ACCOUNT1}" --password ./passwd.txt \
    --port ${PORT} --mine --minerthreads 1 \
    --maxpeers 5 --nodiscover --nodekey ./bootnode/privatenet1 \
    --rpc --rpcaddr 0.0.0.0 --rpcport ${RPCPORT} --rpcapi "eth,personal,net,admin,wan" --rpccorsdomain '*' \
    --bootnodes "enode://f9b91ac38231ecfb12564a006ad6d97d5d1bdaf1a74134fc4cf08e1d7151f7e18e00181cb94347ce3272d6af79d1ebc8b8bf50ce50b40b013a8ff9cf16ff034a@127.0.0.1:17719" \
    --ipcpath ${IPCPATH} \
    --keystore $DATADIR/keystore \
    --identity LocalTestNode
```
Then copy the genesis hash from the logging

```js
INFO [02-20|20:52:42] Loaded most recent local header          number=0 hash=0x5073fc1eb41a4d1414f50cb6693863f9c3ebb794d34057b0693a5be82fc332db td=1
```

and put it in ```params/config.go```. rebuild gwan and start it again.

### Lastly to make privacy tx work you need to create a few OTAs first. you can use the initializeota script for that.

```js
loadScript("./loadScript/initializeota.js")
```

This will initialize 9 OTAs for each allowed privacy tx value:

```
var tranValues = [ 10, 20, 50, 100, 200, 500, 1000, 5000, 50000 ];

```
