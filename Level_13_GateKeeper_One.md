# Level 13 : GateKeeper One

**Ethernaut Level 14 : GateKeeper Two:** 

From the Solidity Docs about [Global Variables](https://docs.soliditylang.org/en/latest/cheatsheet.html?#global-variables) we know that `gasleft() returns (uint256)`is a function that returns the **remaining gas** left for the transaction.

In Ethereum, computations cost money. This is calculated by `gas * gas price`, where `gas`  is a unit of computation and `gas price` scales with the load on Ethereum network.

Things get overcomplicated here because to pass the `gateTwo`checks you have to call `level.enter{gas: exactAmountOfGas}(gateKey)`with a very specific amount of gas that will make `gasleft().mod(8191)`return `0`(the gas left must be a multiple of 8191).

The smart move is to just bruteforce the function and increment the gas in each function call until one of the values hits the spot.

We know that the gas used by the `enter` transaction must be at least 8191 plus all the gas spent to execute those opcodes. We can make a range guess and brute force it until it works. This is the code example:

```
for (uint256 i = 0; i < 300; i++) {
            (bool success, ) = address(level13).call{gas: i + (8191 * 3)}(abi.encodeWithSignature("enter(bytes8)", _gateKey));
            if (success) {
                break;
            }
        }
```

Part 3 : 

At this point, we need to find one `_gateKey` value that satisfies at the same time all these requirements:

```
require(uint32(uint64(_gateKey)) == uint16(uint64(_gateKey)), "GatekeeperOne: invalid gateThree part one");
require(uint32(uint64(_gateKey)) != uint64(_gateKey), "GatekeeperOne: invalid gateThree part two");
require(uint32(uint64(_gateKey)) == uint16(tx.origin), "GatekeeperOne: invalid gateThree part three");
```

When you convert a bigger data type into a smaller one such as **`uint32`** into **`uint16`**, the smaller variable will lose some data. Eg: If I have a variable **`uint32 someVar = 0x12345678`** and I convert it into **`uint16`**, I'll be left with **`0x5678`**.

Similarly, if I wanted to convert **`uint16`** into **`uint32`**, the above value will become **`0x00005678`**.

Let's now talk about Bit masking. This is just the `&` bitwise operation. Eg: `1 AND 0`will be 0. `1 AND 1` will become 1. We will make use of bitmasking when we submit the final key to this gate.

With the above techniques in hand, let's try to clear this gate. Let's assume that we have to send the following value as our key - **`0x B1 B2 B3 B4 B5 B6 B7 B8`**. We are taking 8 bytes because the function **`enter()`** needs an argument of **`bytes8 _gateKey`**.

1. The first statement asks us to satisfy the following condition:We can see that B5 and B6 will be 0.
    
    ```
    0x B5 B6 B7 B8 == 0x 00 00 B7 B8
    
    ```
    
2. The second statement will become:This shows that the bytes B1, B2, B3, and B4 can be anything but 0.
    
    ```
    0x 00 00 00 00 B5 B6 B7 B8 != 0x B1 B2 B3 B4 B5 B6 B7 B8
    
    ```
    
3. The third statement will be:We can see that B7 and B8 will be the last two bytes of the address of our **`tx.origin`**.
    
    ```
    0x B5 B6 B7 B8 == 0x 00 00 (last two bytes of tx.origin)
    
    ```
    
    Therefore, the key will be: 0x ANY_DATA ANY_DATA ANY_DATA ANY_DATA 00 00 SECOND_LAST_BYTE_OF_ADDRESS LAST_BYTE_OF_ADDRESS
    
    Since we need to use our **`tx.origin`** address to build our key, we can use the AND operation to set the value of B5 and B6 to 0, and the last two bytes (FFFF) to our **`tx.origin`**'s last two bytes i.e.,
    
    ```
    bytes8(uint64(tx.origin) & 0xFFFFFFFF0000FFFF
    ```
    
    Here, we are taking **`uint64`** value of **`tx.origin`** since we need 8 bytes and doing an AND operation with the value **`0xFFFFFFFF0000FFFF`**.
    
    Compare it with our 0xB1B2B3B4B5B6B7B8 example shown above. The first four bytes are all **`F`** (not 0), B5 and B6 are 0, and the last two bytes are F because this will help retain our last two address bytes.
    
    This will give us our final key needed to clear the gate.
    
    ```solidity
    // SPDX-License-Identifier: MIT
    pragma solidity ^0.6.0;
    contract HackGate {
        GatekeeperOne level13 = GatekeeperOne(0x4bd63D5C6ab2EF1d0237b61882bED29C7EeaFF6d);
        function Hack() external{
            bytes8 _gateKey = bytes8(uint64(tx.origin)) & 0xFFFFFFFF0000FFFF;
            for (uint256 i = 0; i < 300; i++) {
                (bool success, ) = address(level13).call{gas: i + (8191 * 3)}(abi.encodeWithSignature("enter(bytes8)", _gateKey));
                if (success) {
                    break;
                }
            }
        }
    }
    
    contract GatekeeperOne {
      .....
    }
    ```