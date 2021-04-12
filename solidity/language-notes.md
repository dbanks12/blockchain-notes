# Solidity

## Language Feature Notes

* `view` and `pure` are function decorators signifying "only views contract state, does not update it" or "does not view nor update contract state"
* Built in hash function `keccak256` -> version of SHA3
    * Langauge does not have built-in string comparison. `keccak256` can be used to hash and compare strings.
* A contract function can `emit` information about the contract blockchain via `events`. This is a way to communicate updates to a fronted webapp.
* Mappings map one type to another (like an account address to its balance): `mapping (address => uint) public accountBalance;`
* "In Solidity, there are certain global variables that are available to all functions. One of these is msg.sender, which refers to the address of the person (or smart contract) who called the current function."
* `require` makes it so that a function will throw an error and stop if some condition is not true
* Contracts can inherit from others
* A subcontract inherits `public` functions from supercontract
* Function arguments (and global structs and arrays accessed inside functions) can be declared as `memory` or `storage` to signify whether or not information should be stored on the blockchain
* `memory`, `storage`, and `calldata` are type modifiers that can be used to specify how/where a variable or argument is stored:
    * `storage`: store this value on the blockchain
    * `memory`: stored temporarily in memory, is mutable, can be used for function declaration params and/or function variables
    * `calldata`: stored temporarily, is immutable, can only be used for function declaration params, _must_ be used for dynamic params of an external function
    * TODO: how are these handled different by the compiler or compute node?
* `internal` and `external` are visibility modifiers in addition to `private` and `public`
    * `private`: not callable outside this contract
    * `public`: callable outside this contract
    * `internal`: same as `private` but can also be callable by contracts that inherit from this one
    * `external`: similar to `public`, but can _only_ be called outside this contract
* `interface` is for talking to another contract on the blockchain that we don't own
* A contract will generally have an `_owner` and functions for transferring onwership
* `modifier` keyword lets you define a funcion-like entity that "modifies" a function. Often used to ensure that certain conditions are met before allowing a function to run
* Every single solidity operation costs gas based on complexity. Caller of a contract function (`msg.sender`) pays fees based on the operations in that function
* Normally `uint`s would be stored as 256 bits even if its a `uint8`. But you can pack things into a `struct` to save space
* `now` returns the current unix timestamp of the latest block
* `view` functions don't cost any gas if called externally by a user. If a function is tagged as `view`, that tells web3.js that it only needs to query your local Ethereum node to run the function, and it doesn't need to create a blockchain transaction.
    * If a `view` function is called internally by another function in the same contract that is not `view`, it will still cost gas because the other function creates a transaction on Ethereum and will need to be verified by every node.
* The most expensive resource in Solidity is `storage`. It is often better to reconstruct an array from scratch every time a function is called instead of storing it permanently in the blockchain and costing users gas fees.
* `memory` arrays must be initialized with a length and cannot be resized with `push` as `storage` arrays can. May be changed in future versions of Solidity

