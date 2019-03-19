---
layout: page
author: tyrion70
title: Introduction to migration
---
### About porting
Because Wanchain is based on the Ethereum EVM, porting your application 1-1 is pretty simple. However to fully leverage the power of Wanchain you need to think on switching your application from using native Wancoin to WRC20. If you port an application 1-1, you basically change Ethereum for Wancoin. While this in itself is good if you are just looking to expand your dApp from Ethereum to Wanchain, how much better would it be if your dApp would support both Wanchain, Ethereum and Bitcoin in one application? By switching from the native coin to WRC20 tokens you can leverage all the crosschain options of Wanchain with a few lines of extra code.


### Implementing token support 
With normal Ethereum or Wancoin transactions you create a normal transaction where the value determines the amount of coins transfered from account A to B. With tokens you send a transaction to the token contract to transfer an amount of tokens from address A to address B. By changing the code to interact with a contract instead of sending coins you can still use Ethereum in your dApp while running on the Wanchain network.

### Differences
The changes between Ethereum and Wanchain are documented on [this page](/docs/difference-between-wanchain-and-ethereum)

### List of resources:
- [https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial](https://github.com/ethereum/wiki/wiki/Ethereum-Development-Tutorial)
- [https://medium.com/crowdbotics/building-ethereum-dapps-with-meta-mask-9bd0685dfd57](https://medium.com/crowdbotics/building-ethereum-dapps-with-meta-mask-9bd0685dfd57)
- [https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2](https://medium.com/@mvmurthy/full-stack-hello-world-voting-ethereum-dapp-tutorial-part-1-40d2d0d807c2)
- [https://medium.com/zastrin/build-an-ethereum-dapp-using-ethers-js-c561f9c4dd2f](https://medium.com/zastrin/build-an-ethereum-dapp-using-ethers-js-c561f9c4dd2f)
