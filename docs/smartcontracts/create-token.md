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

<div class="alert alert-info">
  <b>Note</b>: 
  * Privacy transaction function is "otatransfer" in the ERC20 Protocol, the contract with privacy transaction need to implement ERC20 Protocol
  * Privacy balance is stored in the map privacyBalance, function otabalanceOf can get this balance
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


### How To Invoke Privacy Transfer

After deployed above token contract on WANCHAIN,in the WANCHAIN console,you can invoke token privacy transaction according to following process:

Suppose there are at lease 3 accounts in your WANCHAIN node

1. Define asset function and variable

    ```js
    var initPriBalance = 10000;
    var priTranValue = 888;
    var wanBalance = function (addr) {
        return web3.fromWin(web3.eth.getBalance(addr));
    }
    var wanUnlock = function (addr) {
        return personal.unlockAccount(addr, "wanglu", 99999);
    }
    var sendWanFromUnlock = function (From, To, V) {
        eth.sendTransaction({ from: From, to: To, value: web3.toWin(V) });
    }
    var wait = function (conditionFunc) {
        var loopLimit = 130;
        var loopTimes = 0;
        while (!conditionFunc()) {
            admin.sleep(2);
            loopTimes++;
            if (loopTimes >= loopLimit) {
                throw Error("wait timeout! conditionFunc:" + conditionFunc)
            }
        }
    }
    wanUnlock(eth.accounts[1])
    wanUnlock(eth.accounts[2])
    stampBalance = 0.09;
    ```

2. buy stamp for token privacy transaction
    
    ```js
    abiDefStamp = [{ "constant": false, "type": "function", "stateMutability": "nonpayable", "inputs": [{ "name": "OtaAddr", "type": "string" }, { "name": "Value", "type": "uint256" }], "name": "buyStamp", "outputs": [{ "name": "OtaAddr", "type": "string" }, { "name": "Value", "type": "uint256" }] }, { "constant": false, "type": "function", "inputs": [{ "name": "RingSignedData", "type": "string" }, { "name": "Value", "type": "uint256" }], "name": "refundCoin", "outputs": [{ "name": "RingSignedData", "type": "string" }, { "name": "Value", "type": "uint256" }] }, { "constant": false, "type": "function", "stateMutability": "nonpayable", "inputs": [], "name": "getCoins", "outputs": [{ "name": "Value", "type": "uint256" }] }];
    contractDef = eth.contract(abiDefStamp);
    stampContractAddr = "0x00000000000000000000000000000000000000c8";
    stampContract = contractDef.at(stampContractAddr);
    var wanAddr = wan.getWanAddress(eth.accounts[1]);
    var otaAddrStamp = wan.generateOneTimeAddress(wanAddr);
    txBuyData = stampContract.buyStamp.getData(otaAddrStamp, web3.toWin(stampBalance));
    sendTx = eth.sendTransaction({ from: eth.accounts[1], to: stampContractAddr, value: web3.toWin(stampBalance), data: txBuyData, gas: 1000000 });
    wait(function () { return eth.getTransaction(sendTx).blockNumber != null; });
    
    keyPairs = wan.computeOTAPPKeys(eth.accounts[1], otaAddrStamp).split('+');
    privateKeyStamp = keyPairs[0];
    ```

3. get stamp mix set for ring sign
    
    ```js
    var mixStampAddresses = wan.getOTAMixSet(otaAddrStamp, 2);
    var mixSetWith0x = []
    for (i = 0; i < mixStampAddresses.length; i++) {
        mixSetWith0x.push(mixStampAddresses[i])
    }
    ```

4. define token contract ABI
    
    ```js
    var erc20simple_contract = web3.eth.contract([{ "constant": true, "inputs": [], "name": "name", "outputs": [{ "name": "", "type": "string" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": false, "inputs": [{ "name": "_spender", "type": "address" }, { "name": "_value", "type": "uint256" }], "name": "approve", "outputs": [{ "name": "success", "type": "bool" }], "payable": false, "stateMutability": "nonpayable", "type": "function" }, { "constant": true, "inputs": [], "name": "totalSupply", "outputs": [{ "name": "", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": false, "inputs": [{ "name": "_to", "type": "address" }, { "name": "_toKey", "type": "bytes" }, { "name": "_value", "type": "uint256" }], "name": "otatransfer", "outputs": [{ "name": "", "type": "string" }], "payable": false, "stateMutability": "nonpayable", "type": "function" }, { "constant": false, "inputs": [{ "name": "_from", "type": "address" }, { "name": "_to", "type": "address" }, { "name": "_value", "type": "uint256" }], "name": "transferFrom", "outputs": [{ "name": "success", "type": "bool" }], "payable": false, "stateMutability": "nonpayable", "type": "function" }, { "constant": true, "inputs": [], "name": "decimals", "outputs": [{ "name": "", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": true, "inputs": [{ "name": "", "type": "address" }], "name": "privacyBalance", "outputs": [{ "name": "", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": true, "inputs": [{ "name": "_owner", "type": "address" }], "name": "balanceOf", "outputs": [{ "name": "balance", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": true, "inputs": [], "name": "symbol", "outputs": [{ "name": "", "type": "string" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": false, "inputs": [{ "name": "initialBase", "type": "address" }, { "name": "baseKeyBytes", "type": "bytes" }, { "name": "value", "type": "uint256" }], "name": "initPrivacyAsset", "outputs": [], "payable": false, "stateMutability": "nonpayable", "type": "function" }, { "constant": false, "inputs": [{ "name": "_to", "type": "address" }, { "name": "_value", "type": "uint256" }], "name": "transfer", "outputs": [{ "name": "success", "type": "bool" }], "payable": false, "stateMutability": "nonpayable", "type": "function" }, { "constant": true, "inputs": [{ "name": "_owner", "type": "address" }], "name": "otabalanceOf", "outputs": [{ "name": "balance", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": true, "inputs": [{ "name": "_owner", "type": "address" }, { "name": "_spender", "type": "address" }], "name": "allowance", "outputs": [{ "name": "remaining", "type": "uint256" }], "payable": false, "stateMutability": "view", "type": "function" }, { "constant": true, "inputs": [{ "name": "", "type": "address" }], "name": "otaKey", "outputs": [{ "name": "", "type": "bytes" }], "payable": false, "stateMutability": "view", "type": "function" }, { "anonymous": false, "inputs": [{ "indexed": true, "name": "_from", "type": "address" }, { "indexed": true, "name": "_to", "type": "address" }, { "indexed": false, "name": "_value", "type": "uint256" }], "name": "Transfer", "type": "event" }, { "anonymous": false, "inputs": [{ "indexed": true, "name": "_owner", "type": "address" }, { "indexed": true, "name": "_spender", "type": "address" }, { "indexed": false, "name": "_value", "type": "uint256" }], "name": "Approval", "type": "event" }]);
    
    contractAddr = '0xa2e526a3632d225f15aa0592e00bed31a48c953d';
    // this address should changed according to your contract deploy
    erc20simple = erc20simple_contract.at(contractAddr)
    ```

5. create one time address for account1
    
    ```js
    var wanAddr = wan.getWanAddress(eth.accounts[1]);
    var otaAddrTokenHolder = wan.generateOneTimeAddress(wanAddr);
    keyPairs = wan.computeOTAPPKeys(eth.accounts[1], otaAddrTokenHolder).split('+');
    privateKeyTokenHolder = keyPairs[0];
    addrTokenHolder = keyPairs[2];
    sendTx = erc20simple.initPrivacyAsset.sendTransaction(addrTokenHolder, otaAddrTokenHolder, '0x' + initPriBalance.toString(16), { from: eth.accounts[1], gas: 1000000 });
    wait(function () { return eth.getTransaction(sendTx).blockNumber != null; });
    
    ota1Balance = erc20simple.privacyBalance(addrTokenHolder)
    if (ota1Balance != initPriBalance) {
        throw Error('ota1 balance wrong! balance:' + ota1Balance + ', except:' + initPriBalance)
    }
    ```

6. generate ring sign data
    
    ```js
    var hashMsg = addrTokenHolder
    var ringSignData = personal.genRingSignData(hashMsg, privateKeyStamp, mixSetWith0x.join("+"))
    ```

7. create one time address for account2
    
    ```js
    var wanAddr = wan.getWanAddress(eth.accounts[2]);
    var otaAddr4Account2 = wan.generateOneTimeAddress(wanAddr);
    keyPairs = wan.computeOTAPPKeys(eth.accounts[2], otaAddr4Account2).split('+');
    privateKeyOtaAcc2 = keyPairs[0];
    addrOTAAcc2 = keyPairs[2];
    ```

8. generate token privacy transfer data
    
    ```js
    cxtInterfaceCallData = erc20simple.otatransfer.getData(addrOTAAcc2, otaAddr4Account2, priTranValue);
    ```

9. generate call token privacy transfer data

    ```js
    glueContractDef = eth.contract([{ "constant": false, "type": "function", "inputs": [{ "name": "RingSignedData", "type": "string" }, { "name": "CxtCallParams", "type": "bytes" }], "name": "combine", "outputs": [{ "name": "RingSignedData", "type": "string" }, { "name": "CxtCallParams", "type": "bytes" }] }]);
    glueContract = glueContractDef.at("0x0000000000000000000000000000000000000000")
    combinedData = glueContract.combine.getData(ringSignData, cxtInterfaceCallData)
    ```

10. send privacy transaction
    
    ```js
    sendTx = personal.sendPrivacyCxtTransaction({from:addrTokenHolder, to:contractAddr, value:0, data: combinedData, gasprice:'0x' + (200000000000).toString(16)}, privateKeyTokenHolder)
    wait(function(){return eth.getTransaction(sendTx).blockNumber != null;});
    ```

11. check balance
    
    ```js
    ota2Balance = erc20simple.privacyBalance(addrOTAAcc2);
    if (ota2Balance != priTranValue) {
        throw Error("ota2 balance wrong. balance:" + ota2Balance + ", expect:" + priTranValue);
    }
    ota1Balance = erc20simple.privacyBalance(addrTokenHolder)
    if (ota1Balance != initPriBalance - priTranValue) {
        throw Error("ota2 balance wrong. balance:" + ota1Balance + ", expect:" + (initPriBalance - priTranValue));
    }
    ```
