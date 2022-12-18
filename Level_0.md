# Level 0 : Hello Ethernaut

Here the main objective is to play with your javascript console

Prefer using await `contract.info()`

than `contract.info()` or `contract.infoNum().then(v => v.toString())`

⇒ Follow the path

### Key Security Takeaways

- If you implement a fallback function, **keep it simple**
- Use fallback functions to **emit payment events to the transaction log**
- Use fallback functions to **check simple conditional requirements**
- **Think twice** before using fallback functions to change contract ownership, transfer funds, support low-level function calls, and more.