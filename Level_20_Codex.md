# Level 20 : Denial

Main Objective is to prevent the owner from withdrawing the funds when they call the `withdraw()` function. 

Low levels functions forward the gas on different manners :

- call() forwards all the gas along with the call unless a gas value is specified
    - it returns 2 values : bool success showing if the call succeeded and a bytes memory data which contains the return value
- Transfer() and send() functions only forwards 2300 gas

Here the key point is that the return values of the external calls are not checked anywhere

To exploit the contract and prevent the **`owner.transfer(amountToSend)`** from being called, we need to create a contract with a **`fallback`** or **`receive`** function that drains all the gas and prevents further execution of the **`withdraw()`** function.

⇒ To implement it, we just have to call the setWithDrawPartner() to make the address of our deployed contract the partner.  Then define a receive() function with an infinite loop to drain all the gas.

## **Takeaways**

Always check the return value of low-level calls, especially in cases where the called address is controlled by a third party.

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Level20{
    Denial level20_ = Denial(payable(0x69e0b40C1f5D0c3f0BB9157A1838eff679A63314));
    constructor() {
        level20_.setWithdrawPartner(address(this));
    }
    receive() external payable {
        while (true) {}
    }
}

contract Denial {
...
}
```