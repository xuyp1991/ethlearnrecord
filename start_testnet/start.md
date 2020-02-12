# 以太坊测试链搭建

学习以太坊上面的相关开发，私链搭建是第一步，如果直接在公链上进行操作的话会消耗以太坊代币，代价比较大，本文描述一下如何在个人电脑上搭建一条以太坊私链

所在平台：ubuntu18.04

所需工具：geth

## 搭建步骤

### 定义genesis.json

作为区块链来说创始区块是很重要的，搭建私链的第一步就是定义创世区块参考: https://github.com/ethereum/go-ethereum/wiki/Private-network

```genesis.json
{
  "config": {
      "chainId": 100,
      "homesteadBlock": 0,
      "eip150Block": 0,
      "eip150Hash": "0x0000000000000000000000000000000000000000000000000000000000000000",
      "eip155Block": 0,
      "eip158Block": 0,
      "byzantiumBlock": 0,
      "constantinopleBlock": 0,
      "petersburgBlock": 0,
      "istanbulBlock": 0,
      "ethash": {}
    },
  "nonce": "0x0",
  "timestamp": "0x5ddf8f3e",
  "extraData": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "gasLimit": "0x47b760",
  "difficulty": "0x00002",
  "mixHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x0000000000000000000000000000000000000000",
  "alloc": {
      "0x1e82968C4624880FD1E8e818421841E6DB8D1Fa4" : {"balance" : "30000000000000000000"}
    },
  "number": "0x0",
  "gasUsed": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000"
}
```

### 启动私链

编辑好genesis.json就可以通过genesis.json启动私链了。首先看一下相关目录结构

```tree
├── geth
├── testnet
│ ├── genesis,json
│ ├── data
```

启动命令

```bash
./geth --datadir ./testnet/data init ./testnet/genesis.json
```

登陆控制台

```bash
./geth --datadir ./testnet/data --networkid 100 console
```

其中 networkid 就是 genesis.json 中配置的 chainId

### 创建用户

创建并查看账户

```bash
> personal.newAccount() 
> eth.accounts
```

### 挖矿

刚创建的用户是没有代币的，所以需要挖矿来获得代币

挖矿命令

```bash
> miner.start()
```

等待几分钟然后停止挖矿

```bash
> miner.stop()
```

查看挖到的代币

```bash
> acc0 = eth.accounts[0]
> eth.getBalance(acc0) 
2.56e+21
> web3.fromWei(eth.getBalance(acc0), 'ether')
2560
```

这段时间挖到了2560个代币

### 转账

#### 创建接收代币的账户

```bash
>  personal.newAccount()
> acc1 = eth.accounts[1]
> web3.fromWei(eth.getBalance(acc1), 'ether') 
0
```

#### 转账1 ETH

```bash
>  val = web3.toWei(1)
 "1000000000000000000"
 > eth.sendTransaction({from: acc0, to: acc1, value: val})  # 报错,因为账户未解锁
 > personal.unlockAccount(acc0)
 > eth.sendTransaction({from: acc0, to: acc1, value: val})
 > web3.fromWei(eth.getBalance(acc0), 'ether')
 2560
 > web3.fromWei(eth.getBalance(acc1), 'ether')
 0
```

由结果可以看出转账以后代币并没有到acc1账户上面，因为矿工没有把转账操作执行并记录在区块中

#### 挖矿

```bash
> miner.start()

> miner.stop()
```

#### 查看余额

```bash
>  web3.fromWei(eth.getBalance(acc0), 'ether')
2579
>  web3.fromWei(eth.getBalance(acc1), 'ether')
1
```

## 总结

这篇文档我们启动了一条以太坊的私链挖矿并进行了转账操作，为以后进行合约开发做了一些准备工作。