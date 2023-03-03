# Vault

> 解锁 vault，走上人生巅峰

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Vault {
  bool public locked;
  bytes32 private password;

  constructor(bytes32 _password) {
    locked = true;
    password = _password;
  }

  function unlock(bytes32 _password) public {
    if (password == _password) {
      locked = false;
    }
  }
}
```

这道题的解法一目了然，就是读取到password的值然后变更locked状态。

常见的解法有两种

## 从构造中读取

  在合约创建时候的，**字节码最后会拼接上构造合约需要的参数**，对应到本题，就是`bytes32 _password`.
  在本题中，由于Vault合约是由第三方合约创建，所以直接从etherscan上读取难度较大，这时候我们祭出神器tenderly.

  ![image](./images/vault1.png)
  
  从这里可以看到在调用`CREATE`指令时候的字节码，我们读取后64位，即代表了password的参数.

  `412076657279207374726f6e67207365637265742070617373776f7264203a29`
  
 ## 使用ethers.js来读取插槽里面的数据

  所有链上数据都是公开透明的，所以即使是没有被设置为public的数据，我们也可以用ethersjs中的`getStorageAt`的方法来进行读取合约中的私有变量。
  
  在Solidity的内存布局中有插槽（slot）的概念。
  
  每一个合约，都有`2^256`个内存插槽用于存储状态变量，但是这些插槽并不是实际存在的。也就是说，并没有实际占用了这么多空间，而是按需分配，用到时就会分配，不用时就不存在。
  
  插槽数量的上限是`2^256`，每个插槽的大小是32个字节(即bytes32)。
  
  对于本身就占据32字节的定长类型的数据，如本题中的bool与bytes32，均占用一个插槽，所以我们需要读取的私有变量`password`位于slot1
    
  我们使用`ethers.js`来读取slot1:
  
```js
const provider = new ethers.providers.AnkrProvider("goerli");
await provider2.getStorageAt("0x42B5fdd4Ba45aE453994cE8cff7aAB7fd8669730", 1);
```
最后成功解锁了vault，走上了人生巅峰

参考文章：

[深入理解合约升级](https://mirror.xyz/xyyme.eth/5eu3_7f7275rqY-fNMUP5BKS8izV9Tshmv8Z5H9bsec)
  
  
  
  
  
