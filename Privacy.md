# Privacy

> 解锁宝藏，走上人生巅峰

> 这道题算是之前Vault的升级版

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Privacy {

  bool public locked = true;
  uint256 public ID = block.timestamp;
  uint8 private flattening = 10;
  uint8 private denomination = 255;
  uint16 private awkwardness = uint16(block.timestamp);
  bytes32[3] private data;

  constructor(bytes32[3] memory _data) {
    data = _data;
  }
  
  function unlock(bytes16 _key) public {
    require(_key == bytes16(data[2]));
    locked = false;
  }

  /*
    A bunch of super advanced solidity algorithms...

      ,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`
      .,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,
      *.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^         ,---/V\
      `*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.    ~|__(o.o)
      ^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'^`*.,*'  UU  UU
  */
}
```

参考[vault](https://github.com/magiconch/ethernaut-/blob/main/Vault.md)的题解，由于uint8 + uint8 + uint16 <= bytes32，所以他们共占用一个插槽。

因此对于本题，data所占用的插槽在slot4。（因为bytes32[3]也是定长的）

```solidity
function convert(bytes32 val) external pure returns(bytes16) {
      return bytes16(val);
}

```

获得bytes16转换后的结果：`0xe54d823d82467f377a7a556374e81e67`









