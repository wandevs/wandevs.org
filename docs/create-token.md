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

```js
{% include_relative solidity/token.sol %}
```

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

```js
{% include_relative solidity/simple-contract1.sol %}
```

```js
        var erc20simple = erc20simple_contract.new(
            {
                from: web3.eth.accounts[1],
                data: '0x6060604052341561000f57600080fd5b6111e38061001e6000396000f3006060604052600436106100d0576000357c0100000000000000000000000000000000000000000000000000000000900463ffffffff16806306fdde03146100d5578063095ea7b31461016357806318160ddd146101bd578063209194e6146101e657806323b872dd146102e4578063313ce5671461035d57806341267ca21461038657806370a08231146103d357806395d89b4114610420578063a3796c15146104ae578063a9059cbb14610533578063ce6ebd3d1461058d578063dd62ed3e146105da578063f8a5b33514610646575b600080fd5b34156100e057600080fd5b6100e86106f8565b6040518080602001828103825283818151815260200191508051906020019080838360005b8381101561012857808201518184015260208101905061010d565b50505050905090810190601f1680156101555780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b341561016e57600080fd5b6101a3600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091908035906020019091905050610731565b604051808215151515815260200191505060405180910390f35b34156101c857600080fd5b6101d06108b5565b6040518082815260200191505060405180910390f35b34156101f157600080fd5b610269600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803590602001908201803590602001908080601f016020809104026020016040519081016040528093929190818152602001838380828437820191505050505050919080359060200190919050506108bb565b6040518080602001828103825283818151815260200191508051906020019080838360005b838110156102a957808201518184015260208101905061028e565b50505050905090810190601f1680156102d65780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b34156102ef57600080fd5b610343600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803573ffffffffffffffffffffffffffffffffffffffff16906020019091908035906020019091905050610a75565b604051808215151515815260200191505060405180910390f35b341561036857600080fd5b610370610ce5565b6040518082815260200191505060405180910390f35b341561039157600080fd5b6103bd600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610cea565b6040518082815260200191505060405180910390f35b34156103de57600080fd5b61040a600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610d02565b6040518082815260200191505060405180910390f35b341561042b57600080fd5b610433610d4b565b6040518080602001828103825283818151815260200191508051906020019080838360005b83811015610473578082015181840152602081019050610458565b50505050905090810190601f1680156104a05780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b34156104b957600080fd5b610531600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803590602001908201803590602001908080601f01602080910402602001604051908101604052809392919081815260200183838082843782019150505050505091908035906020019091905050610d84565b005b341561053e57600080fd5b610573600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091908035906020019091905050610e21565b604051808215151515815260200191505060405180910390f35b341561059857600080fd5b6105c4600480803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610f7e565b6040518082815260200191505060405180910390f35b34156105e557600080fd5b610630600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190803573ffffffffffffffffffffffffffffffffffffffff16906020019091905050610fc7565b6040518082815260200191505060405180910390f35b341561065157600080fd5b61067d600480803573ffffffffffffffffffffffffffffffffffffffff1690602001909190505061104e565b6040518080602001828103825283818151815260200191508051906020019080838360005b838110156106bd5780820151818401526020810190506106a2565b50505050905090810190601f1680156106ea5780820380516001836020036101000a031916815260200191505b509250505060405180910390f35b6040805190810160405280600d81526020017f57616e546f6b656e2d426574610000000000000000000000000000000000000081525081565b6000808214806107bd57506000600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054145b15156107c557fe5b81600260003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167f8c5be1e5ebec7d5bd14f71427d1e84f3dd0314c0f7b2291e5b200ac8c7c3b925846040518082815260200191505060405180910390a36001905092915050565b60005481565b6108c36110fe565b81600360003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020541015610947576040805190810160405280601481526020017f73656e64657220746f6b656e20746f6f206c6f770000000000000000000000008152509050610a6e565b81600360003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254039250508190555081600360008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254019250508190555082600460008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000209080519060200190610a34929190611112565b506040805190810160405280600781526020017f737563636573730000000000000000000000000000000000000000000000000081525090505b9392505050565b600081600160008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205410158015610b42575081600260008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000205410155b15610cd95781600160008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254019250508190555081600160008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254039250508190555081600260008673ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600082825403925050819055508273ffffffffffffffffffffffffffffffffffffffff168473ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a360019050610cde565b600090505b9392505050565b601281565b60036020528060005260406000206000915090505481565b6000600160008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020549050919050565b6040805190810160405280600881526020017f57616e546f6b656e00000000000000000000000000000000000000000000000081525081565b80600360008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000208190555081600460008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000209080519060200190610e1b929190611112565b50505050565b600081600160003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054101515610f735781600160003373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff1681526020019081526020016000206000828254039250508190555081600160008573ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020600082825401925050819055508273ffffffffffffffffffffffffffffffffffffffff163373ffffffffffffffffffffffffffffffffffffffff167fddf252ad1be2c89b69c2b068fc378daa952ba7f163c4a11628f55a4df523b3ef846040518082815260200191505060405180910390a360019050610f78565b600090505b92915050565b6000600360008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff168152602001908152602001600020549050919050565b6000600260008473ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002060008373ffffffffffffffffffffffffffffffffffffffff1673ffffffffffffffffffffffffffffffffffffffff16815260200190815260200160002054905092915050565b60046020528060005260406000206000915090508054600181600116156101000203166002900480601f0160208091040260200160405190810160405280929190818152602001828054600181600116156101000203166002900480156110f65780601f106110cb576101008083540402835291602001916110f6565b820191906000526020600020905b8154815290600101906020018083116110d957829003601f168201915b505050505081565b602060405190810160405280600081525090565b828054600181600116156101000203166002900490600052602060002090601f016020900481019282601f1061115357805160ff1916838001178555611181565b82800160010185558215611181579182015b82811115611180578251825591602001919060010190611165565b5b50905061118e9190611192565b5090565b6111b491905b808211156111b0576000816000905550600101611198565b5090565b905600a165627a7a72305820c7a344a3e72215ba3952e15ab354904e1edd02798099defd384453d37142be270029',
                gas: '4700000'
            },
            function (e, contract) {
                console.log(e, contract);
                if (typeof contract.address !== 'undefined') {
                    console.log('Contract mined! address: ' + contract.address + ' transactionHash: ' + contract.transactionHash);
                }
            }
        )
```

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

