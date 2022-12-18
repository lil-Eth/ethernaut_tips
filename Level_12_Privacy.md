# Level 12 : Privacy

**Ethernaut Level 12 : Privacy : Private type variable can be read**

It is a common misconception that `private`type variables cannot be read. Even if your contract is not published, attackers can look at contract transactions to determine values stored in the state of the contract. For this reason, it's important that unencrypted private data is not stored in the contract code or state

how the [Layout of State variables in Storage](https://docs.soliditylang.org/en/v0.8.15/internals/layout_in_storage.html#layout-of-state-variables-in-storage) work.

- **Each storage slot will use 32 bytes (word size)**
- **For each variable, a size in bytes is determined according to its type**
- **Multiple, contiguous items that need less than 32 bytes are packed into a single storage slot if possible according to the following rules:**
    - **The first item in a storage slot is stored lower-order aligned.**
    - **Value types use only as many bytes as are necessary to store them.**
    - **If a value type does not fit the remaining part of a storage slot, it is stored in the next storage slot.**
    - **Structs and array data always start a new slot and their items are packed tightly according to these rules.**
    - **Items following struct or array data always start a new storage slot.**

```solidity
bool public locked = true;
uint256 public ID = block.timestamp;
uint8 private flattening = 10;
uint8 private denomination = 255;
uint16 private awkwardness = uint16(now);
bytes32[3] private data;
```

`unlock` uses the third entry (index 2) of `data` which is a `bytes32` array. Let's determined `data`'s third entry's slot number (each slot can accommodate at most 32 bytes) according to storage rules:

- `locked` is 1 byte `bool` in slot 0
- `ID` is a 32 byte `uint256`. It is 1 byte extra big to be inserted in slot 0. So it goes in & totally fills slot 1
- `flattening` is a 1 byte `uint8`, `denomination` is a 1 byte `uint8` and `awkwardness` is a 2 byte `uint16` totals 4 bytes. So, all three of these go into slot 2
- Array data always start a new slot, so `data` starts from slot 3. Since it is `bytes32` array each value takes 32 bytes. Hence value at index 0 is stored in slot 3, index 1 is stored in slot 4 and index 2 value goes into slot 5

We now know that the **“secret”** key we need to **unlock** the contract is stored in the **fifth slot** of the storage layout of the contract.

Alright so key is in slot 5 (index 2 / third entry). Read it.

This `key` is 32 byte. But `require` check in `unlock` converts the `data[2]` 32 byte value to a `byte16` before matching.

—> `key = await web3.eth.getStorageAt(contract.address, 5)`

⇒ 0x665ac26c921fc1f91fb3d561b41dc1fadcf61e75af2e004a5a5c7db5f87b2e45

`byte16(data[2])` will truncate the last 16 bytes of `data[2]` and return only the first 16 bytes.

Accordingly convert `key` to a 16 byte hex (with prefix - `0x`):

`key = key.slice(0, 34)` ⇒ 0x665ac26c921fc1f91fb3d561b41dc1fa

`await contract.unlock(key)`