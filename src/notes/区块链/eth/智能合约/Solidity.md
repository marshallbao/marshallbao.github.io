# Solidity

### 环境初始化

Hardhat V3

```
# vscode 安装 solidity 插件
# 创建目录；前提是有 nodejs/npm 环境
mkdir hardhat && cd hardhat

# 初始化 npm 项目
npm init -y

# 安装 hardhat
npm install --save-dev hardhat

# 初始化
npx hardhat --init

# 选择 mocha-ethers

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



### 基础知识

合约是可部署到区块链的最小单元， 一个合约通常由**状态变量（合约数据）**和**合约函数**组成。

第一个合约

```
//SPDX-License-Identifier: MIT
pragma solidity >=0.8.0;

// 定义一个合约
contract Counter {
    uint public counter;
    
    constructor() {
        counter = 0;
    }
    
    function count() public {
        counter = counter + 1;
    }
    
    function get() public view returns (uint) {
        return counter;
    }
}
```

声明编译器版本

定义合约

合约构造函数

变量和函数的可见性

​	public/internal/private

定义变量和常量

不可变量

定义函数

状态可变性

​	view/pure/payable

视图函数

数据类型

变量/常量

函数

函数修饰符

| **修饰符** | **是否允许读取状态变量？** | **是否允许修改状态变量？** | **适用于**                     |
| ---------- | -------------------------- | -------------------------- | ------------------------------ |
| **`pure`** | **否**                     | **否**                     | 只进行输入参数的计算。         |
| **`view`** | **是**                     | **否**                     | 读取状态变量，然后返回或计算。 |
| **(无)**   | 是                         | 是                         | 交易函数，可以读取和修改状态。 |