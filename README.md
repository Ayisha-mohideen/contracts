# SimpleStorage — On-Chain Structured Data Storage

> A foundational Ethereum smart contract demonstrating on-chain state management, structured data storage, and getter/setter patterns. Built with Solidity and developed in Remix IDE.

![Solidity](https://img.shields.io/badge/Solidity-^0.8.18-363636?logo=solidity)
![IDE](https://img.shields.io/badge/Built%20with-Remix%20IDE-2b2b2b)
![Network](https://img.shields.io/badge/Deployable-Any%20EVM%20Chain-blue)
![License](https://img.shields.io/badge/License-MIT-green)

---

## Overview

SimpleStorage is a smart contract that stores and retrieves structured data — specifically, a mapping of names to favourite numbers — entirely on-chain. It demonstrates core Solidity concepts: storage layout, structs, dynamic arrays, mappings, and function visibility.

While straightforward in scope, this contract reflects the same patterns that underpin more complex DeFi protocols: deterministic state transitions, gas-efficient data structures, and clean separation between reads and writes.

**Key capabilities:**
- Stores a list of `Person` structs (name + favourite number) in a dynamic array
- Maintains a `string → uint256` mapping for O(1) name lookups
- Exposes typed getter functions for both array and mapping access
- Fully deployable on any EVM-compatible network

---

## Tech Stack

| Tool | Role |
|------|------|
| Solidity `^0.8.18` | Contract language |
| Remix IDE | Development and deployment environment |
| EVM | Target execution environment |

---

## Technical Deep Dive

### Contract architecture

```solidity
contract SimpleStorage {
    struct Person {
        uint256 favouriteNumber;
        string name;
    }

    Person[] public listOfPeople;
    mapping(string => uint256) public nameToFavouriteNumber;

    function addPerson(string memory _name, uint256 _favouriteNumber) public;
    function retrieve() public view returns (uint256);
    function store(uint256 _favouriteNumber) public;
}
```

### Storage layout

Two data structures are maintained in parallel:

- `listOfPeople` — a dynamic `Person[]` array, iterable and index-accessible
- `nameToFavouriteNumber` — a `mapping(string => uint256)` for direct name-based lookup

Both are updated atomically in `addPerson()`, ensuring consistency between the indexed array and the hash map.

### Data location — memory vs storage

`addPerson()` takes `_name` as `memory` (not `storage`) because string arguments passed to external/public functions must be copied into the EVM's memory space. This is a key Solidity concept: `storage` is persistent on-chain state, `memory` is temporary and scoped to the function call. Confusing the two is a common source of both bugs and unnecessary gas costs.

### Why a mapping and an array?

- The **mapping** gives O(1) lookup by name — efficient for "what is Alice's number?" queries
- The **array** preserves insertion order and enables iteration — useful for enumerating all stored people

This dual-structure pattern is commonly used in production contracts (e.g. allowlists, registries) where both lookup and enumeration are needed.

### Gas considerations

- `store()` and `addPerson()` are state-changing (`public` non-view) — each call costs gas
- `retrieve()` and direct mapping/array access via auto-generated getters are `view` functions — free when called off-chain
- Using `uint256` (rather than smaller uints) avoids EVM padding overhead in most contexts

### Upgradeability note

This contract uses direct storage (not a proxy pattern). It is intentionally minimal — a foundation for understanding how Solidity manages state before introducing patterns like upgradeable proxies or diamond storage.

---

## Contract Interface

```solidity
// Store a single favourite number
function store(uint256 _favouriteNumber) public

// Retrieve the stored favourite number
function retrieve() public view returns (uint256)

// Add a person with name and favourite number
function addPerson(string memory _name, uint256 _favouriteNumber) public

// Auto-generated getters
listOfPeople(uint256 index) → (uint256 favouriteNumber, string name)
nameToFavouriteNumber(string name) → uint256
```

---

## Getting Started

**In Remix IDE:**
1. Open [remix.ethereum.org](https://remix.ethereum.org)
2. Create a new file and paste `SimpleStorage.sol`
3. Compile with Solidity `^0.8.18`
4. Deploy to Remix VM (London) for instant local testing
5. Call `addPerson("Alice", 42)` then `nameToFavouriteNumber("Alice")` to verify

**With Foundry (optional):**
```bash
git clone https://github.com/Ayisha-mohideen/simple-storage
cd simple-storage
forge build
forge test
```

---

## What This Demonstrates

| Concept | Where |
|---------|-------|
| Structs | `Person` definition |
| Dynamic arrays | `listOfPeople` |
| Mappings | `nameToFavouriteNumber` |
| Data locations | `memory` in `addPerson()` |
| View vs state-changing functions | `retrieve()` vs `store()` |
| Auto-generated getters | `public` state variables |

---

## Project Status

Complete. First project in the [Cyfrin Updraft](https://updraft.cyfrin.io) Solidity smart contract developer curriculum — the foundation for the FundMe and PriceConverter projects that follow.

---

*Built by [Ayisha](https://github.com/Ayisha-mohideen) · Cyfrin profile: [profiles.cyfrin.io/u/vivacious1574](https://profiles.cyfrin.io/u/vivacious1574)*
