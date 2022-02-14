# Setup A Private Wanchain Chain On Your Local Machine

Follow the steps below to start a single-node Wanchain private chain locally.

*Note: The current version of Wanchain private chain supports evmVersion up to Byzantium, and Solidity version up to 0.8.10. You need to manually set evmVersion to Byzantium for compiling when your solidity version is greater than 0.4.26.*

## 1. Download Gwan

Go to Wanchain Github channel to download the latest go-wanchain binary file:

https://github.com/wanchain/go-wanchain/releases

Please download the correct file according to your local operating system.

You will have an executable file of Gwan after you unzip the downloaded file.

Get the default testing keystore account by click the following URL:

https://raw.githubusercontent.com/wanchain/go-wanchain/develop/bootnode/UTC--2017-05-14T03-13-33.929385593Z--2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e

You can see a json string on your browser after opening the URL above. Save the info to a file locally by "Ctrl+S".

Or, you can use the wget command scripts to get a keystore file beginning with letters UTC, and the wallet address is: 0x2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e.

Save the keystore file to the path `[datadir]/keystore/`, where `[datadir]` is the default data storage directory.

The default value of the private chain is:

~/.wanchain/pluto (linux)

~/Library/Wanchain/pluto (MacOS)

## 2. Create A File With Default Password

Create a pw.txt file. The content of password is Wanchain.

Or, you can use the following scripts to generate this password file in the home directory:


`echo 'Wanchain' > ~/pw.txt`


## 3. Launch the Wanchain Private Chain

Use the following scripts to start the private chain node. Detailed parameters can be obtained from `gwan --help`.

The `--plutodev` in the command below means a private chain, and `--nodiscover` means the turn-off of P2P discovery. The path followed by `--password` parameter is the directory where the pw.txt file is located.

```
./gwan --plutodev --nodiscover --etherbase "0x2d0e7c0813a51d3bd1d08246af2a8a7a57d8922e" --unlock "0x2d0e7c0813a51d3bd1d08246af2a8a7anet57d8922e" --password ~/pw.txt --mine --minerthreads,debug --rethpc --minerthreads=1 --rethpc ,web3,personal,txpool,pos' --rpccorsdomain "*"
```

## 4. Connect Private Chain Node Using MetaMask

The parameters of connecting the local private chain node by MetaMask are as follows:


* RPC URL: http://127.0.0.1:8545

* Chain ID: 6

* Symbol: WAN

## 5. Connect Private Chain Node Using Remix

If you want to use Remix to link the local private chain node, you should choose Web 3 Provider and enter http://127.0.0.1:8545 on the DEPLOY page. After that you will see the default unlock address.

## 6. How to Use Remix To Deploy And Debug Smart Contracts Written By Solidity


