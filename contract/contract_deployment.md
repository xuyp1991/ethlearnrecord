# 以太坊私链上部署合约

本文开发一个以太坊合约，主要是学习在私链上部署合约

平台  ubuntu18.04

链上工具    geth

合约编译工具  solc

## 以太坊合约

### 编写简单的以太坊合约

```multipy.sol
pragma solidity >=0.4.0 <0.7.0;

contract Multiply_contract {

    function multipy(uint x) public  returns (uint) {
        return x*67;
    }
}
```

上面是一个简单的以太坊合约，只有1个功能multipy，传入一个数值，将该数值乘以67以后返回

### 编译以太坊合约

```bash
solc --optimize --bin --abi multiply.sol

Binary:
6080604052348015600f57600080fd5b5060958061001e6000396000f3fe6080604052348015600f57600080fd5b506004361060285760003560e01c80631d1e478d14602d575b600080fd5b604760048036036020811015604157600080fd5b50356059565b60408051918252519081900360200190f35b6043029056fea2646970667358221220a609f6aa44071d9fcaa4029cf2e22264ded758f8858d01ddb793f8fe04ec1b8664736f6c63430006020033
Contract JSON ABI
[{"inputs":[{"internalType":"uint256","name":"x","type":"uint256"}],"name":"multipy","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"nonpayable","type":"function"}]

```

使用工具solc对合约进行编译，--bin生成二进制，--abi 生成abi

### 在以太坊私链上部署合约

```bash
> var test_abi = JSON.parse('[{"inputs":[{"internalType":"uint256","name":"x","type":"uint256"}],"name":"multipy","outputs":[{"internalType":"uint256","name":"","type":"uint256"}],"stateMutability":"nonpayable","type":"function"}]')
undefined

> testContract= web3.eth.contract(test_abi)
{
  abi: [{
      inputs: [{...}],
      name: "multipy",
      outputs: [{...}],
      stateMutability: "nonpayable",
      type: "function"
  }],
  eth: {
    accounts: ["0xe072c6d906ea0f98f1dd9cfd11fc01beed5d2cbc"],
    blockNumber: 370,
    coinbase: "0xe072c6d906ea0f98f1dd9cfd11fc01beed5d2cbc",
    compile: {
      lll: function(),
      serpent: function(),
      solidity: function()
    },
    defaultAccount: undefined,
    defaultBlock: "latest",
    gasPrice: 1000000000,
    hashrate: 0,
    mining: false,
    pendingTransactions: [],
    protocolVersion: "0x40",
    syncing: false,
    call: function(),
    chainId: function(),
    contract: function(abi),
    estimateGas: function(),
    fillTransaction: function(),
    filter: function(options, callback, filterCreationErrorCallback),
    getAccounts: function(callback),
    getBalance: function(),
    getBlock: function(),
    getBlockByHash: function(),
    getBlockByNumber: function(),
    getBlockNumber: function(callback),
    getBlockTransactionCount: function(),
    getBlockUncleCount: function(),
    getCode: function(),
    getCoinbase: function(callback),
    getCompilers: function(),
    getGasPrice: function(callback),
    getHashrate: function(callback),
    getHeaderByHash: function(),
    getHeaderByNumber: function(),
    getMining: function(callback),
    getPendingTransactions: function(callback),
    getProof: function(),
    getProtocolVersion: function(callback),
    getRawTransaction: function(),
    getRawTransactionFromBlock: function(),
    getStorageAt: function(),
    getSyncing: function(callback),
    getTransaction: function(),
    getTransactionCount: function(),
    getTransactionFromBlock: function(),
    getTransactionReceipt: function(),
    getUncle: function(),
    getWork: function(),
    iban: function(iban),
    icapNamereg: function(),
    isSyncing: function(callback),
    namereg: function(),
    resend: function(),
    sendIBANTransaction: function(),
    sendRawTransaction: function(),
    sendTransaction: function(),
    sign: function(),
    signTransaction: function(),
    submitTransaction: function(),
    submitWork: function()
  },
  at: function(address, callback),
  getData: function(),
  new: function()
}

```

上面的操作是通过abi生成一份contract对象

```bash
> testcode="0x6080604052348015600f57600080fd5b5060958061001e6000396000f3fe6080604052348015600f57600080fd5b506004361060285760003560e01c80631d1e478d14602d575b600080fd5b604760048036036020811015604157600080fd5b50356059565b60408051918252519081900360200190f35b6043029056fea2646970667358221220a609f6aa44071d9fcaa4029cf2e22264ded758f8858d01ddb793f8fe04ec1b8664736f6c63430006020033"
"0x6080604052348015600f57600080fd5b5060958061001e6000396000f3fe6080604052348015600f57600080fd5b506004361060285760003560e01c80631d1e478d14602d575b600080fd5b604760048036036020811015604157600080fd5b50356059565b60408051918252519081900360200190f35b6043029056fea2646970667358221220a609f6aa44071d9fcaa4029cf2e22264ded758f8858d01ddb793f8fe04ec1b8664736f6c63430006020033"
> web3.eth.estimateGas({data: testcode})
85625

```

生成code对象并计算合约需要的gas

```bash
> personal.unlockAccount("0xe072c6d906ea0f98f1dd9cfd11fc01beed5d2cbc", '123456')
true
> testContractIns = testContract.new({data: testcode, gas: 1000000, from: "0xe072c6d906ea0f98f1dd9cfd11fc01beed5d2cbc"}, function(e, contract){
......          if(!e){
.........              if(!contract.address){
............                console.log("Contract transaction send: Transaction Hash: "+contract.transactionHash+" waiting to be mined...");
............              }else{
............                console.log("Contract mined! Address: "+contract.address);
............                console.log(contract);
............              }
.........            }else{
.........              console.log(e)
.........            }
......         })
INFO [02-13|15:12:07.949] Submitted contract creation              fullhash=0x86c6d381e77c4c205838eeb8bc718a0bf23b3ca66a997e97c0e0439c635c04be contract=0x84240E9C8c66b895B168119794AbB0FeeDcA9C4F
Contract transaction send: Transaction Hash: 0x86c6d381e77c4c205838eeb8bc718a0bf23b3ca66a997e97c0e0439c635c04be waiting to be mined...
{
  abi: [{
      inputs: [{...}],
      name: "multipy",
      outputs: [{...}],
      stateMutability: "nonpayable",
      type: "function"
  }],
  address: undefined,
  transactionHash: "0x86c6d381e77c4c205838eeb8bc718a0bf23b3ca66a997e97c0e0439c635c04be"
}
```

解锁账户并部署合约，合约部署以后需要上链才能使用，接下来私链需要挖矿产生新的区块才能使合约上链

```bash
miner.start()
......
Contract mined! Address: 0x84240e9c8c66b895b168119794abb0feedca9c4f
......
miner.stop()
```

出现Contract mined!说明上链成功

```bash
> testContractIns.multipy.call(6)
402
```

使用testContractIns.multipy.call(6)进行调用，返回402


