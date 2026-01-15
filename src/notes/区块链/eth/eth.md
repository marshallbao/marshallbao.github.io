# ETH

## 概念

以太坊

以太坊是一个由世界各地的计算机组成的网络，遵循一套称为以太坊协议的规则。以太坊网络提供了一个基础，任何人都可以在上面构建和使用社区、应用程序、组织和数字资产。

EVM

以太坊虚拟机 (EVM) 是一个去中心化虚拟环境，它在所有以太坊节点上一种安全一致地方式执行代码。 节点运行以太坊虚拟机，以执行智能合约，利用“[燃料](https://ethereum.org/zh/gas/)”度量执行[操作](https://ethereum.org/zh/developers/docs/evm/opcodes/)所需的计算工作，从而确保高效的资源分配和网络安全性。

智能合约

智能合约是存在于以太坊区块链上的计算机程序。它们仅在由用户发出的交易触发时执行。智能合约使以太坊在功能方面非常灵活。这些程序充当去中心化软件和组织的构建基块。

[Solidity](https://soliditylang.org/) 是以太坊虚拟机 (EVM) 最流行的区块链编程语言，也广泛用于一系列与 EVM 兼容的区块链

dapp

分布式 app，不单指 eth 的 dapp，其他链也有；只要是后端是部署在区块链的上就行，表现形式可以是智能合约也可以是链的功能模块；



参考

https://ethereum.org/zh/

https://ethereum.org/zh/glossary/

https://ethereum.org/zh/developers/docs/

https://www.binance.com/en/square/post/3335663067090

https://eth-dev-docs.readthedocs.io/en/latest/begin/index.html



ETH 上的账户

以太坊中有两类账户：

（1）**外部用户账户（EOAs）**——该类账户由公钥-私钥对控制（由人控制）。

（2）**合约账户**（**CA：Contract Account**）——该类账户由存储在账户中的代码控制。



### ETH 上的交易

以太坊交易可以分为以下三种类型：

1. **普通交易**：用于向其他地址转移以太币。
2. **创建合约**：用于在区块链上创建智能合约。这种交易类型的`to`字段为空，`data`字段包含智能合约的字节码。
3. **调用合约函数**：用于调用已部署的智能合约的函数。这种交易类型的`to`字段包含目标合约地址，`data`字段包含函数名称和参数。







