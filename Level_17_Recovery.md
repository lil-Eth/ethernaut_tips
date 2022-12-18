# Level 17 : Recovery

**Here are two methods for finding the contract address, via raw sender information and Etherscan, respectively.**

1. `**address = rightmost_20_bytes(keccak(RLP(sender address, nonce)))**`
    - `**sender address`: is the contract or wallet address that created this new contract**
    - `**nonce`: is the number of transactions sent from the `sender address` OR, if the sender is a factory contract, the `nonce` is the number of contract-creations made by this account.**
    - `**RLP`: is an encoder on data structure, and is the default to serialize objects in Ethereum.**
    - `**keccak`: is a cryptographic primitive that compute the Ethereum-SHA-3 (Keccak-256) hash of any input.**

`address public a = address(keccak256(0xd6, 0x94, YOUR_ADDR, 0x01));`

1. Use Etherscan
    1. In Etherscan, look up your current contract by address.
    2. Inside the `Internal Txns` tab, locate the latest contract creation, and click on the link into the new contract.
    3. The new contract address should now show at the top left hand corner.

⇒ Use Remix.ide to create the same contrat (copy/paste)

⇒ then compile it but to deploy only use the “AtAddress” button and then call the destruct function