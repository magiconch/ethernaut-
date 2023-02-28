# Vault

> 解锁 vault，走上人生巅峰

```sol
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

- 直接读取构造时候的bytes
  在合约创建时候的字节码最后，会拼接上构造合约需要的参数，对应到本题，就是`bytes32 _password`.
  在本题中，由于Vault合约是由第三方合约创建，所以直接从etherscan上读取难度较大，这时候我们祭出神器tenderly.

  ![image](./images/vault1.png)
  
  从这里可以看到在调用`CREATE`指令时候的字节码，我们读取后64位，即代表了password的参数.

  `412076657279207374726f6e67207365637265742070617373776f7264203a29`
  
-  使用ethers.js来读取插槽里面的数据

  
