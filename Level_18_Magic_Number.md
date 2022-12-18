# Level 18 : Magic Number

**Ethernaut Level 18 : MagicNumber**

In Douglas Adams' *The Hitchhiker's Guide to the Galaxy*, 42 is the number from which all meaning ("the meaning of life, the universe, and everything") can be derived. However, its actual significance is open to interpretation.

To solve this, there's a size restriction of 10 opcodes, i.e., 10 bytes since each opcode is 1 byte. Therefore, our solver should be of at most 10 bytes and it should return 42 (0x2a).

We need to write two sets of bytecodes:

1. Initialization bytecode: It is responsible for preparing the contract and returning the runtime bytecode.
2. Runtime bytecode: This is the actual code run after the contract creation. In other words, this contains the logic of the contract.

### Runtime ByteCode

The code needs to return the 32 byte magic number - 42 or `0x2a` (in hex).

OpCode for RETURN takes 2 arguments :

- location of value in memory
- size of this value to be returned

To store a value in memory we need to use MSTORE which also takes 2 arguments : 

- location of value in stack
- its size

To push a value and a size into stack we need to use PUSH1 opcode

```
OPCODE       NAME
------------------
 0x60        PUSH1
 0x52        MSTORE
 0xf3        RETURN
```

1. Push 0x2a in stack ⇒ 602a
2. Push 0x50 in stack ⇒ 6050
3. store value ⇒ MSTORE = 52
4. Push 0x20 in stack (32 bytes = size of 0x2a) ⇒ 6020
5. Push 0x50 in stack (the slot where 0x52 is stored) ⇒ 6050
6. RETURN value ⇒ f3

Concatenated opcode ⇒ 602a60505260206050f3

### Initialization ByteCode

Following opcodes are needed for the above purpose:

```
OPCODE       NAME
------------------
 0x60        PUSH1
 0x52        MSTORE
 0xf3        RETURN
 0x39        CODECOPY // Takes 3 arguments : destination/current position/size of code in bytes
```

1. Push 0x0a (runtime opcode of 10 bytes) in stack for size of CODECOPY ⇒ 600a
2. Push (--) in stack for position of CODECOPY ⇒ 60--
3. Push 0x00 in stack for destination of CODECOPY ⇒ 6000
4. Use CODECOPY ⇒ 39
5. Push 0x0a in stack for size of RETURN opcode ⇒ 600a
6. Push 0x00 in stack for position of param to RETURN opcode ⇒ 6000
7. Return value ⇒ f3

Concatenated opcode ⇒ 600a60--600039600a6000f3

We can replace -- by 12 because Runtime Bytecode start 12 bytes after the initialization bytecode opcode so concatenated opcode ⇒  600a600c600039600a6000f3

### Final Opcode : 600a600x600039600a6000f3602a60505260206050f3

```jsx
bytecode = '600a600c600039600a6000f3602a60505260206050f3'
txn = await web3.eth.sendTransaction({from: player, data: bytecode})
solverAddr = txn.contractAddress
await contract.setSolver(solverAddr)
```