# Solidity

## Language Feature Notes

* Commenting standard in the Solidity community is `natspec`
    * `/// @` for contract and function `natspec` comment tags
    * Every function at the very least should have a `@dev` tag explaining what it does for developers
    * None of the tags are mandatory of course, but they are best practice
* A contract will generally have an `_owner` and functions for transferring ownership
* `view` and `pure` are function decorators signifying "only views contract state, does not update it" or "does not view nor update contract state"
* Built in hash function `keccak256` -> version of SHA3
    * Language does not have built-in string comparison. `keccak256` can be used to hash and compare strings.
* A contract function can `emit` information about the contract blockchain via `events`. This is a way to communicate updates to a fronted webapp.
* Mappings map one type to another (like an account address to its balance): `mapping (address => uint) public accountBalance;`
* "In Solidity, there are certain global variables that are available to all functions. One of these is msg.sender, which refers to the address of the person (or smart contract) who called the current function."
* `require` makes it so that a function will throw an error and stop if some condition is not true
    * refunds gas cost to caller on failure
    * preferable over `assert`
* `assert` will throw an error if false
    * does not refund gas cost to caller on failure
    * typically used when something has gone horribly wrong (like a `uint` overflow)
* Contracts can inherit from others
* A subcontract inherits `public` functions from supercontract
* Contracts can leverage multiple inheritance of other contracts or interfaces
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
* `interface` is the same as an interface in other programming languages - list of functions (basically an API) that an inheriting contract must implement
    * In Solidity, `interfaces` are also used for talking to other contracts on the blockchain that we don't own
* `modifier` keyword lets you define a function-like entity that "modifies" a function. Often used to ensure that certain conditions are met before allowing a function to run
    * Basically a "decorator" as it is called in Python
* Every single Solidity operation costs gas based on complexity. Caller of a contract function (`msg.sender`) pays fees based on the operations in that function
* `view` functions don't cost any gas if called externally by a user. If a function is tagged as `view`, that tells web3.js that it only needs to query your local Ethereum node to run the function, and it doesn't need to create a blockchain transaction.
    * If a `view` function is called internally by another function in the same contract that is not `view`, it will still cost gas because the other function creates a transaction on Ethereum and will need to be verified by every node.
* The most expensive resource in Solidity is `storage`. It is often better to reconstruct an array from scratch every time a function is called instead of storing it permanently in the blockchain and costing users gas fees.
* `memory` arrays must be initialized with a length and cannot be resized with `push` as `storage` arrays can. May be changed in future versions of Solidity
* `payable` function modifier specifies a function that can receive Ether - call the function and pay Ether at the same time
    * `msg.value` is the amount of Ether sent to the function call
    * `ether` is a built-in unit of measurement in Solidity
    * After Ether is sent to a contract, it is stored in the contract's Ethereum account and is trapped there unless you add a function to withdraw Ether from the contract
        * To send Ether to an address  (like `_owner`), the address variable must be cast to `address payable`
            * Casting any integer to an address makes it payable - hence `address(uint160(owner()));`
* Normally `uint`s would be stored as 256 bits even if its a `uint8`. But you can pack things into a `struct` to save space
* `now` returns the current unix timestamp of the latest block
* `payableAddress.transfer(address(this).balance)` - `address(this).balance` is the full balance of the contract

* `ERC721` - I think this is a common NFT interface/API - indivisible tokens
* Can keep track of addresses approved for spending a certain token using a `mapping (tokenId => address)`. For a given token, compare that address to `msg.sender`
* Adding a function modifier to a function does not actually change its prototype. Or at least it does not break API if that function is implementing an interface that omits that function modifier.
    * I can add a modifier `onlyOwnerOf(_tokenId)` to my implementation of the `ERC721` `approve()` definition while remaining compatible with the interface
* General good practice is to add checks to prevent users from sending to address 0
* A `library` is a special type of contract
    * Allows use of the `using` keyword which automatically adds functions in the library as members of the specified type (e.g. `uint` in `using <lib> for uint`)
    * When a library function is called, it is called via `<arg1>.func(<arg2>,...)`. In essence the function becomes a member of the type.
        * Note this is similar to `self` or `cls` syntax in Python for methods of an object or class
    * SafeMath is a library that prevents simple issues like over/underflows
        * Useful for attaching functions to native data types: `using SafeMath for uint256;` (inside `contract{}`);
        * Implements `add`, `sub`, `mul`, `div`

## Web3.js

* When you want to call a function on a smart contract you need to query an Ethereum node and tell it:
    * The address of the smart contract
    * The function to call
    * The args to pass it
* Ethereum nodes only speak `JSON-RPC`, but Web3.js hides those queries beneath the surface
* Install via something like:
    * `npm install web3`
    * `yarn add web3`
    * `bower install web3`
    * or download the minified `.js` file and include in the project via
        * `<script language="javascript" type="text/javascript" src="web3.min.js"></script>`

### Web Provider

* Every DApp needs a Web3 Provider - an Ethereum node to send reads/writes to
    * Can run your own Ethereum node
    * Or use Infura
        * Could use Infura to access Ethereum nodes with a caching layer for fast reads
        * Free API
        * Set up Web3 to use Infura as the web3 provider:
            * `var web3 = new Web3(new Web3.providers.WebsocketProvider("wss://mainnet.infura.io/ws"));`
    * For writes, there needs to be some private-key/public-key management... `metamask`

### Metamask

* Metamask injects their `web3` provider into the browser in the global JavaScript object `web3`
    * Can just check for its existence in JS
    * Mist browser handles this similarly/identically?

### Talking to Contracts from Web3

* Need 2 things to talk to a contract - its address and ABI (Application Binary Interface)
    * ABI is a JSON representation of a contract's functions for use by Web3.js
        * Generated by the Solidity compiler
        * Instantiate it in Web3 via:
            * `var myContract = new web3js.eth.Contract(myABI, myContractAddress);`

## Resources
* OpenZeppelin
* Uniswap source code
* Aave source code
