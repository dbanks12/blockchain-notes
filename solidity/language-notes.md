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
* `internal` and `external` are visibility modifiers in addition to `private` and `public`
    * `private`: not callable outside this contract
    * `public`: callable outside this contract
    * `internal`: same as `private` but can also be callable by contracts that inherit from this one
    * `external`: similar to `public`, but can _only_ be called outside this contract
* `interface` is for talking to another contract on the blockchain that we don't own
