# Level 6 : Delegation

**Delegate call is a special, low level function call intended to 
invoke functions from another, often library, contract.**

****The main advantage of `delegatecall()` is that you can preserve your current, calling contract’s context. 

This context includes its `storage`and its `msg.sender`, `msg.value`attributes.

**—> w*hen contract A makes a delegatecall to Contract B, it allows Contract B to freely mutate its storage A.***

Here we need trigger the fallback function in the Delegation contract to invoke the `pwn()`
 function via `msg.data.`

If we send a transaction to the contract Delegation the fallback function will be triggered. The main point is to make [`msg.data`](http://msg.data) our call to `pwn()`.

For that we have the web3 library on javascript : pwn() = **`web3.eth.abi.encodeFunctionSignature("pwn()”) —> 0xdd365b8b`**

With what we learned in the previous level with this we can easily send this transaction : **`sendTransaction({from: player, to: contract.address, data: web3.eth.abi.encodeFunctionSignature("pwn()")})`**

# **Key Security Takeaways**

- Use the higher level `call()` function to inherit from libraries, especially when you i) don’t need to change contract storage and ii) do not care about gas control.
- When inheriting from a library intending to alter your contract’s storage, make sure to line up your storage slots with the library’s storage slots to avoid these edge cases.
- Authenticate and do conditional checks on functions that invoke delegatecalls.
- [Read solidity documentation](https://solidity.readthedocs.io/en/v0.4.24/security-considerations.html) on security considerations.