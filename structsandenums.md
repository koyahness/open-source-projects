# Solidity

In Solidity, Structs and Enums are user-defined types that help you organize complex data and state logic.

They are essential for making your code readable and, more importantly, for optimizing how data is stored in the EVM's 32-byte slots.

## 1. Enums (Enumerations)
   
Enums are used to create custom types with a finite set of constant values. They are internally treated as integers (starting at 0), which makes them extremely gas-efficient.

 * Gas Impact: An enum with fewer than 256 options fits into a uint8 (1 byte).

 * Readability: They replace "magic numbers" (like using 0 for Pending and 1 for Shipped) with human-readable names.
<!-- end list -->
```
contract Shipping {
    // Define the Enum
    enum Status { 
        Pending,   // 0
        Shipped,   // 1
        Accepted,  // 2
        Rejected,  // 3
        Canceled   // 4
    }

    Status public status; // Defaults to the first element (Pending)

    function ship() public {
        status = Status.Shipped;
    }
    
    function reset() public {
        delete status; // Resets to Status.Pending (Index 0)
    }
}
```
## 2. Structs

Structs allow you to create custom data containers that group different types together. This is the foundation of building complex systems like NFT metadata or user profiles.

Example: Storage-Packed Struct

As we discussed in the foundation of storage, the order of variables inside a struct matters for Gas Packing.

```
struct User {
    uint256 id;      // Slot 0 (32 bytes)
    address wallet;  // Slot 1 (20 bytes) \_ Packed together 
    bool isActive;   // Slot 1 (1 byte)  /  into 32 bytes
    uint8 level;     // Slot 1 (1 byte) /
}

mapping(address => User) public users;

function addUser(uint256 _id) public {
    // Creating a struct in memory first
    User memory newUser = User({
        id: _id,
        wallet: msg.sender,
        isActive: true,
        level: 1
    });
    
    users[msg.sender] = newUser; // Copying from memory to storage
}
```
## 3. Comparing Structs and Enums

| Feature | Enum | Struct |
|---|---|---|
| Purpose | State management / Options | Data grouping |
| Underlying Type | uint8 (usually) | Collection of types |
| Default Value | First element (index 0) | All members set to their defaults |
| Gas Cost | Very low (1 byte) | Variable (based on members) |

## 4. Key Considerations

## Structs in Mappings and Arrays

You will often see structs used inside mappings. Note that you cannot return a whole mapping of structs at once; you must return a single struct by its key or use a loop (which is gas-expensive).

## Memory vs. Storage Structs

When you interact with a struct in a function, you must specify the location:
 * User storage user = users[msg.sender]; — Any changes made to user will directly update the blockchain state.
 * User memory user = users[msg.sender]; — Creates a temporary copy. Changes will disappear when the function ends unless you save them back to storage.

## Combining Structs and Enums

The most common pattern is to include an Enum inside a Struct to track the status of a specific object.
struct Order {
    address buyer;
    uint256 amount;
    Status status; // Using the Enum defined earlier
}
