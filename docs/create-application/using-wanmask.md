---
layout: page
author: tyrion70
title: Using Wanmask
---
## Using WanMask for your dApp

### What is WanMask

One of the fastest ways to start building your own dApp is using WanMask. WanMask (https://wanmask.io) is a browser extension that allows users to interact with the Wanchain Blockchain from their browser. Its also a Wanchain wallet that allows you to store your Wancoin and cross-chain tokens safely. It also supports hardware wallets.

The most important aspect of WanMask is that it allows you to interact with smart contracts which gives you as a Wanchain Developer the opportunity to have them interact with your dApp in their browser without having the burden of loading a complete wallet.

WanMask includes a safe signing process where users are asked to confirm transactions first. It was ported from MetaMask and has the same options that MetaMask has. In the future also privacy transactions and cross-chain functionality will be added to it to further enhance the possibilities you have with your dApp.

### Getting WanMask

Getting WanMask is as easy as going to https://wanmask.io and click the install extension button. That takes you to the right link in the Chrome store to install the extension. Follow the instructions given to you by the extension to setup your first wallet.

### Funding your WanMask

You can fund your WanMask on Testnet using the Wanchain Faucets 
- http://52.88.191.131:8080/ 
- https://wanfaucet.net/testnet/

You could also run a local dev node and point your WanMask there, thats outside of the scope of this document though.

### Wan3

WanMask works by injecting a JavaScript object on every page your user loads. This object is called a wan3 object, just like MetaMask injects a web3 object. Wan3 is a Javascript API that allows you to interact with the Wanchain Blockchain.

In your dApp there is a number of steps you need to take:
- Check if WanMask is installed
- Check if the account is locked
- Check if there is enough balance for the tx you want to do
- Perform the tx and check the result

Below you will find example code for all these steps. If you install WanMask and fund your account you can try it from within this documentation. Click the result tab in each of the examples to see the output.

#### Check if its installed
  
<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="tyrion70" data-slug-hash="XGEpKy" data-preview="true" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="WanMask Demo">
  <span>See the Pen <a href="https://codepen.io/tyrion70/pen/XGEpKy/">
  WanMask Demo</a> by Peter van Mourik (<a href="https://codepen.io/tyrion70">@tyrion70</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### Check if the account is locked
<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="tyrion70" data-slug-hash="vPRgOE" data-preview="true" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="WanMask Demo">
  <span>See the Pen <a href="https://codepen.io/tyrion70/pen/vPRgOE/">
  WanMask Demo</a> by Peter van Mourik (<a href="https://codepen.io/tyrion70">@tyrion70</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### Check balance of account
<p class="codepen" data-height="265" data-theme-id="0" data-default-tab="js,result" data-user="tyrion70" data-slug-hash="BbrppR" data-preview="true" style="height: 265px; box-sizing: border-box; display: flex; align-items: center; justify-content: center; border: 2px solid black; margin: 1em 0; padding: 1em;" data-pen-title="WanMask Demo 3">
  <span>See the Pen <a href="https://codepen.io/tyrion70/pen/BbrppR/">
  WanMask Demo 3</a> by Peter van Mourik (<a href="https://codepen.io/tyrion70">@tyrion70</a>)
  on <a href="https://codepen.io">CodePen</a>.</span>
</p>
<script async src="https://static.codepen.io/assets/embed/ei.js"></script>

#### Expand your dApp
You've seen the basics to interact with your dApp, now your imagination is the limit!

