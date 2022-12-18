# Level 15 : Naugh Coin

**[ERC20](https://ethereum.org/en/developers/docs/standards/tokens/erc-20/)** token standard is an API for tokens that defines certain standard function calls, parameters, and events and anyone who intends to create an ERC20 token must follow those standards. This makes it easier for all the developers using these tokens to predict their usage and interactions.

There are several ways to transfer tokens out of a contract. The `transfer()`function is one of the methods and the other one is `approve()` and `transferFrom()`.

Since the Naught Coin contract is inheriting from ERC20, and the modifier `lockTokens()`is not enforcing the timelock on the `transferFrom()`function, we are free to call the approve and transferFrom to transfer all the tokens out of our account.

- You can use both `transfer()` and `transferFrom()` to move tokens around
- The `lockTokens()` modifier is only applied to the `transfer()` function290897
- `Approve()` and `transferFrom()` functions are also available in Remix IDE

This means you can move tokens around, bypassing lockTokens, using the advised `approve+transferfrom`combination.

1.  Check your account balance or the INITIAL_SUPPLY variable , then invoke `approve()` with your own address and the *exact* account balance.
2. Invoke `transferFrom()` with the parameters: 
    1. your player address
    2. an arbitrary external wallet
    3. the account balance.
3. Submit the instance back into the level to pass.

Key Security Takeaways

- **When interfacing with contracts or implementing an ERC interface, implement all available functions.**

**Answer :** 

`await contract.INITIAL_SUPPLY().then(v => v.toString()) ⇒ 1000000000000000000000000`

`await contract.approve(player,"1000000000000000000000000")`

`await contract.transferFrom(player,contract.address,"1000000000000000000000000")`