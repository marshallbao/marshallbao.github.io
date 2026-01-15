# EVM

### 智能合约

工具

hardhat

Hardhat 是一个用于以太坊和其他兼容链上智能合约开发的开源开发框架。



Remix 

Remix 是一个基于浏览器的，以太坊智能合约开发环境，专门用于编写、调试、测试、部署和管理智能合约



流程

编写 ---> 编译 ---> 部署

以 hardhat 为例子

```
# vscode 安装 solidity 插件
# 创建目录；前提是有 nodejs/npm 环境
mkdir hardhat && cd hardhat

# 初始化 npm 项目
npm init -y

# 安装 hardhat，目前有 v3 版本了，最好指定下版本，下面的命令都是基于 v2 版本的命令
npm install --save-dev hardhat
npm install --save-dev hardhat@2.26.0

# 初始化
npx hardhat init

# 安装部署合约的插件
npm install --save-dev @nomicfoundation/hardhat-ignition-ethers

# 编译
npx hardhat compile

# 配置网络
hardhat.config.js

# 部署
npx hardhat ignition deploy ./ignition/modules/Counter.js --network irishubuat

# 调用
npx hardhat run scripts/call_counter.js --network irishubuat


```



evm json-rpc

```
## 关于合约调用，主要有以下 3 种方法，区别是 
eth_call: 只调用只读函数，合约/链的状态没有变化
eth_sendRawTransaction: 离线交易，也就是将进行签名好的交易发送到链上去（等同于 iris tx evm raw ）
eth_sendTransaction: 写函数，改变合约的状态，需要消耗 gas 以及要进行签名

# eth_call
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_call","params":[{"to": "0xa14A965B587666067914D12f8b4c27c35b43d6a7","data": "0xa87d942c"},"latest"],"id":1}' localhost:8545

# eth_sendRawTransaction
curl -X POST -H "Content-Type: application/json" --data '{"jsonrpc":"2.0","method":"eth_sendRawTransaction","params":["0x01f86d821a20278545d964b8008307a12094a14a965b587666067914d12f8b4c27c35b43d6a78084d09de08ac080a02ae200a08b368b84724d1809d45f1bd35840e9cc7598643edf3428c284bec434a06832f9c9720e39445d98a4f43eac3ca3aad905e22395de942da424b29e981566"],"id":1}' localhost:8545

# eth_sendTransaction
```



关于 eth_call 和 eth_sendTransaction 都有一个字段 data/input

data 字段的内容是方法签名和编码参数的哈希值。

```
chatgpt 解释（以下内容有待确认！！！）
在调用 Ethereum 合约的特定方法时，data 字段用于携带要执行的函数的签名和参数。在这个上下文中，“Hash of the method signature and encoded parameters” 具体指的是如何构建这个 data 字段。以下是对这个过程的详细解释：

1. 合约方法的签名
合约方法的签名通常由方法名称和参数类型组成。例如，假设我们有一个名为 transfer 的方法，它的签名为 transfer(address,uint256)，这里 address 和 uint256 是参数类型。

2. 计算方法签名的哈希
我们需要计算这个方法签名的Keccak-256哈希（Ethereum使用Keccak-256作为哈希函数）。对于签名 transfer(address,uint256)，计算的步骤如下：
把签名字符串转换为字节数组，然后使用 Keccak-256 哈希函数进行哈希运算，得到的哈希值前四个字节将作为方法选择器。

3. 编码参数
接下来，我们需要对函数参数进行编码。ETH合约中使用的是ABI（Application Binary Interface）编码格式。

对于 transfer(address,uint256) 方法，如果我们调用时传入的参数为 0xAbc...123（地址）和 100（数量），我们要做的是：
地址类型需要填充到32字节，即 0x0000000000000000000000000Abc...123
uint256 类型也填充到32字节，即 0x0000000000000000000000000000000000000000000000000000000000000064（因为100对应16进制为0x64）
最终编码的格式如下：

methodHash（4字节） + 参数1（32字节） + 参数2（32字节）

4. 组合成完整的data
将以上所有元素组合在一起，形成最终的 data 字段。例如，假设 methodHash 为 0xa9059cbb，则 data 看起来像这样：

0xa9059cbb + 0x0000000000000000000000000Abc...123 + 0x0000000000000000000000000000000000000000000000000000000000000064

另外，如果没有参数，只有函数名，直接计算函数名的 hash 值，然后取前四个字节即可；

```

参考

https://docs.moonbeam.network/cn/builders/dev-env/hardhat/

https://hardhat.org/hardhat-runner/docs/getting-started#overview

https://ethereum.org/en/developers/docs/apis/json-rpc/



其他库和工具

**ethers.js** 是一个用于与以太坊及其兼容链（如 Binance Smart Chain 等）进行交互的 JavaScript 库。

hardhat 是 js 语言编写的；为什么可以编译 solidity ？















#### 合约标准

**ERC20**

erc20 是一种同质化 token, token之间是完全等价的.

**ERC721**

erc721 是一种非同质化 token (Non-Fungible Token，简称 NFT), token 之间是完全独立的, token 是由链下传入的一个 uint256 类型的数字. 

**ERC1155**

erc1155 严格来说, 它并不是 token, 它是一种合约规范, 它所定义的接口的是为了更方便的管理多种代币.

假设有如下场景: 为了项目需要, 现在要发 10 种 erc20 token 和 10 种 erc721 token, 相当于要部署 20 个合约. 而且在调用这 20 个合约时, 是相互独立的, 不能同时对 2 种以上的代币进行转账等操作.

这时候 erc1155 规范(或者说协议), 是专门设计出来管理多个 erc20, erc721 合约的.



**ABI**

参考

https://learnblockchain.cn/article/3870