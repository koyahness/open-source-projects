# Solidity structure

Solidity files follow a specific, rigid hierarchy. Understanding this structure is essential because the compiler reads the file in a specific order to manage inheritance, libraries, and global settings.

## 1. The Global Header
   
Every Solidity file must start with two specific lines that tell the compiler how to treat the code.

 * ***SPDX License Identifier***: Machine-readable code that specifies the legal licensing (e.g., MIT, Apache-2.0).
   
 * ***Pragma Directive***: Tells the compiler which version of Solidity to use. Using ^0.8.0 means "any version from 0.8.0 up to (but not including) 0.9.0."

<!-- end list -->
```
// SPDX-License-Identifier: MIT

pragma solidity ^0.8.20;
```

## 2. External Dependencies (Imports)
   
After the header, you import other files or libraries. This keeps your main contract clean and allows you to reuse verified code (like OpenZeppelin).

```
import "@openzeppelin/contracts/access/Ownable.sol";

import "./MyLibrary.sol";
```

## 3. The Contract Body
   
A contract is similar to a "Class" in object-oriented programming. Inside the contract curly braces, the components should ideally follow this order for readability:

### A. State Variables

These are stored on-chain in Storage.

 * ###Constants###: uint256 public constant MAX_SUPPLY = 1000;
   
 * ###Immutables###: address public immutable owner;

 * ###State Variables###: uint256 public count;
   
### B. Type Definitions & Data Structures

 * ###Events###: For off-chain logging.
   
 * Errors: For gas-efficient reverts (preferred over require strings in 0.8.4+)
   
 * Structs: Custom data groupings.
   
 * Enums: Custom discrete types (e.g., Status { Pending, Shipped, Delivered }).

## 4. Function Order (The "Style Guide")
   
To make code easier for auditors to read, functions should be grouped by their "visibility" and "nature":

 * Constructor: Runs only once during deployment.
   
 * Receive/Fallback: Special functions for handling plain Ether transfers.
   
 * External: Functions called only from outside the contract.
   
 * Public: Can be called internally and externally.
   
 * Internal: Only available to this contract and those inheriting from it.
   
 * Private: Only available to this specific contract.

<!-- end list -->

```
contract StructureExample {
    // 1. State Variables
    uint256 private _value;

    // 2. Events
    event ValueChanged(uint256 newValue);

    // 3. Constructor
    constructor(uint256 initialValue) {
        _value = initialValue;
    }

    // 4. External Functions
    function setValue(uint256 newValue) external {
        _value = newValue;
        emit ValueChanged(newValue);
    }

    // 5. View/Pure Functions
    function getValue() external view returns (uint256) {
        return _value;
    }
}
```

## 4. Inheritance Structure
   
Solidity supports multiple inheritance. When a contract inherits from others, the order matters because of the C3 Linearization (the order in which the compiler resolves which function to use if multiple parents have the same function name).

 * Rule: List parents from "most base-like" to "most derived."
 * Keyword: Use is to inherit.
<!-- end list -->

contract MyContract is Context, Ownable, ERC20 {
    // Logic here...
}

###  Key Takeaway

A well-structured contract isn't just about aesthetics; it directly impacts gas costs (via variable packing) and security (by making the flow of logic clear to auditors).
