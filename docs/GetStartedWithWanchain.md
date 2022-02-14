---
layout: page
author: tyrion70
title: Get Started With Wanchain
---

## Quick Start
For the web3.js library, you can create a local Web3 instance and set the provider to connect to Wanchain (HTTP are supported):

```
const Web3 = require('web3'); //Load Web3 library
//Create local Web3 instance - set Wanchain as provider
const web3 = new Web3("https://gwan-ssl.wandevs.org:56891"); 
```

For the ethers.js library, define the provider by using ethers.providers.StaticJsonRpcProvider(providerURL, {object}) and setting the provider URL to Wanchain:

```
const ethers = require('ethers');


const providerURL = "https://gwan-ssl.wandevs.org:56891";
// Define Provider
const provider = new ethers.providers.StaticJsonRpcProvider(providerURL, {
    chainId: 888,
    name: 'wanchain'
});
```

Any Ethereum wallet should be able to generate a valid address for Wanchain (for example, MetaMask).

## Chain ID
The Wanchain Mainnet chain ID is: **888**, testnet chain ID is: **999**.

## Block Explorers
For Wanchain, you can use any of the following block explorers:

https://wanscan.org

https://testnet.wanscan.org

## Connect MetaMask
If you already have MetaMask installed, you can easily connect MetaMask to Wanchain:

If you do not have MetaMask installed, or would like to follow a tutorial to get started, please check out the Interacting with Wanchain using MetaMask guide. 

https://metamask.io

https://chainlist.org

If you want to connect MetaMask by providing the network information, you can use the following data:

* Network Name: Wanchain

* RPC URL: https://gwan-ssl.wandevs.org:56891

* ChainID: 888

* Symbol (Optional): WAN

* Block Explorer (Optional): https://wanscan.org/

## Testnet

* Network Name: Wanchain Testnet

* RPC URL: https://gwan-ssl.wandevs.org:46891

* ChainID: 999

* Symbol (Optional): WAN

* Block Explorer (Optional): https://testnet.wanscan.org/

1234567

