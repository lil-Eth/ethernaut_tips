# Level 2 : Fallout

With no method to switch ownership of the contract we have to look deeper on the contract.

Specially on the first line , the constructor.

Contract name is `Fallout` and contract constructor is `Fal1out().`

Seems stupid but usual on contracts when writing too fast or speaking with colleagues on discord and writing smart contracts.. haha 😟

Only thing to do is call `await contract.Fal1out()` to be the owner.

**Takeaways :** 

- **Practice test driven development**. Recommended: [Truffle framework](https://truffleframework.com/) offers built in testing for your next DApp.
- **Heed compiler warnings**, and make sure you are using the latest compiler version.
- **[Use security analysis tools](https://consensys.github.io/smart-contract-best-practices/security_tools/)**, which are usually free and good at finding simple human errors.