---
layout: page
author: tyrion70
title: Create token
---
## How To Deploy Smart Contract On Wanchain

### Smart Contract Source Code Example

<div class="alert alert-info">
  <b>Note</b>: The following smart contract code is only an example and is NOT to be used in Production systems.
</div>


The standard token contract can be quite complex. But in essence a very basic token boils down to this:

<button type="button" class="btn btn-info" data-toggle="collapse" data-target="#fiddle1">View Code</button>
<div class="collapse" id="fiddle1">
<iframe src="https://ethfiddle.com/services/iframesnippet/zFXdifNZZ9" scrolling="no" frameborder="0" height="300" width="300" allowtransparency="true" class="ef_embed_iframe" style="width: 100%; overflow: hidden;"></iframe>
</div>


### How To Compile And Deploy


Requirement:

1. A working Wanchain client, go to the github site: `go-wanchain <https://github.com/wanchain/go-wanchain>`_ to get the latest version
2. `Remix <https://remix.ethereum.org>`_ which is an amazing online smart contract development IDE
3. your awesome Dapp consists of one or multiple smart contracts

Steps:

1. go to remix, copy and paste your smart contract code, make static syntax analysis, and compile it
2. click Details on the right panel of remix, copy all the code of WEB3DEPLOY section from the pop-up
3. copy the script and run it in gwan console

    <button type="button" class="btn btn-info" data-toggle="collapse" data-target="#source1">View Code</button>
    <div id="source1" class="collapse highlight">
        <pre class="highlight">
            {% include_relative includes/simple-contract1.js %}
        </pre>
    </div>
    
4. the transaction id and contract address (hash values starting with '0x') will be printed out onto the console after few seconds
5. now, you can play with your Dapp

<div class="alert alert-info">
  <b>Note</b>: 
  You can locate a demo WANCHAIN token contract and involved scripts under contracts/demo/ directory
</div>

