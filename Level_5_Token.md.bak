# Level 5 : Token

**Interger overflow and Underflow**

Like C and C++, Solidity is a lower level coding language that doesn’t have failsafes for handling storage limitations. This is different than what Ruby and Python developers might be used to.

Ethereum’s smart contract storage slot are each 256 bits, or 32 bytes. Solidity supports both signed integers, and unsigned integers `uint` of up to 256 bits.

This means your arithmetic operations are prone to underflow and overflow errors, when your numbers flow under or over the allocated bits of storage.

![Untitled](Level%205%20Token%20ba031a86483c4990a2c79e8af215adc0/Untitled.png)

The point of failure here is `require(balances[msg.sender] - _value >= 0);`

If we substract at how many we have more than we have we will have an enormous value like the example above.

Here we know that we have 20 tokens, so we juste have to substract 21.

Let’s try`contract.transfer(”random Address”,21)` #because we have 20 tokens so we need less than 0.

To find a random address let’s just go here and select one [goerli.etherscan.io](https://goerli.etherscan.io/).

**Key Security Takeaways**

- Use the [SafeMath.sol](https://github.com/OpenZeppelin/openzeppelin-solidity/blob/master/contracts/math/SafeMath.sol) to handle simple math operations