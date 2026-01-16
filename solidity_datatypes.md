# Solidity

Solidity is a statically typed language, meaning the type of each variable must be specified.

These types are broadly categorized into Value Types (passed by value) and Reference Types (passed by reference/location).

## 1. Value Types
Value types are simple data pieces. When you pass them to a function or assign them to a new variable, a copy of the data is created.
Integers
 * ***uint / int***: Unsigned (positive only) and Signed integers.
 * ***Sizes***: Available from 8 bits to 256 bits (e.g., uint8, uint16 ... uint256).
 * ***Default***: uint is an alias for uint256.
Booleans
 * ***bool***: Can only be true or false.
 * ***Operators***: !, &&, ||, ==, !=.
Addresses
This is unique to Solidity and represents an Ethereum account or contract address (20 bytes).
 * ***address***: Holds a 20-byte value.
 * ***address payable***: Same as address but includes transfer and send methods for moving Ether.
 * ***Members***: .balance (returns Wei), .code (returns bytecode).
Fixed-Size Byte Arrays
 * ***bytes1 to bytes32***: Fixed-length sequences of bytes.
 * ***Efficiency***: Much cheaper than the dynamic bytes type because the size is known.
Enums
 * User-defined labels for integers (as discussed previously).

## 2. Reference Types
Reference types do not store the value directly. Instead, they store a "pointer" to the location of the data. You must explicitly state the Data Location (storage, memory, or calldata) for these types.
Arrays
 * Fixed-size: uint[5] myFixedArray;
 * Dynamic: uint[] myDynamicArray;
 * Members: .length, .push(), .pop().
Structs
 * Custom defined groupings of different types.
Mappings
 * Syntax: mapping(KeyType => ValueType)
 * Behavior: Think of it as a hash table. You cannot iterate over a mapping or get a list of its keys; you must know the key to get the value.

## 3. Variable Scopes
Where a variable is declared determines its lifetime, cost, and visibility.

| Scope | Location | Persistence | Gas Cost |
|---|---|---|---|
| State Variables | Storage | Permanent (on-chain) | Highest |
| Local Variables | Memory / Stack | Temporary (during call) | Low |
| Global Variables | Blockchain | N/A (Blockchain Info) | Very Low / Free |
Common Global Variables
These are provided by the EVM and give information about the blockchain:
 * msg.sender: The address currently calling the function.
 * msg.value: Amount of Wei sent with the call.
 * block.timestamp: Current block's Unix timestamp.
 * block.number: Current block height.
 * tx.origin: The original wallet that started the transaction (use with caution for security).

## 4. Visibility Modifiers
These define who can "see" or call a variable/function.
 * public: Accessible internally and externally. For state variables, Solidity automatically generates a "getter" function.
 * private: Only accessible within the current contract.
 * internal: Like private, but also accessible by contracts that inherit from this one.
 * external: (Functions only) Can only be called from outside the contract.

## 5. Constant & Immutable
Use these to save massive amounts of gas for variables that don't change.
 * constant: Value must be hardcoded at compile time. It is replaced in the bytecode and uses zero storage slots.
 * immutable: Value can be set once inside the constructor, then it never changes. Also uses zero storage slots.
<!-- end list -->

```
uint256 public constant TAX_RATE = 5; // Set at compile time
address public immutable owner;       // Set at deployment

constructor() {
    owner = msg.sender;
}
```

 To master Solidity, we need to look under the hood at how the EVM (Ethereum Virtual Machine) actually treats these types. Here is an exhaustive breakdown of the technical details for each section.
 
## 1. Value Types (Deep Dive)

Value types are stored directly in the stack (a 1024-deep list of 32-byte words) during execution.
Integers: Precision and Overflow
 * Step Sizes: You can use uint8 up to uint256 in increments of 8.
 * Gas Trap: Using uint8 instead of uint256 for a single state variable actually costs more gas. The EVM operates on 32-byte words; to process a uint8, it must first mask the other 24 bytes with zeros. Only use smaller types when packing them into a struct or array.
 * Unsigned (uint) vs Signed (int): int uses "Two’s Complement" representation, meaning the first bit indicates the sign.
Addresses: The Power of call
 * Address vs. Address Payable: address is a 20-byte hex value. address payable is the same but allows access to .transfer(amount) and .send(amount).
 * Conversion: You can convert an address to payable using payable(addr).
 * Members: * <address>.balance: Returns the balance in Wei (10^{18} Wei = 1 ETH).
   * <address>.code: Returns the smart contract bytecode at that address (empty for wallets/EOAs).
Fixed Bytes (bytes1 to bytes32)
 * If you know the length of your data, always use fixed bytes over string or dynamic bytes.
 * bytes32 is the most gas-efficient because it perfectly fills one EVM word/slot.

## 2. Reference Types (Memory & Storage)

Reference types are too large to fit in the 32-byte stack, so they are stored in Memory or Storage.
Mappings: The "Virtual" Hash Table
 * No Iteration: You cannot do map.length or loop through a mapping. If you need to track keys, you must store them in a separate address[] array.
 * Infinite Initialization: Technically, every possible key in a mapping "exists" and is initialized to the default value (0, false, etc.).
 * Storage Only: Mappings can only exist in storage. You cannot create a mapping inside a function (in memory).
Arrays: Fixed vs. Dynamic
 * delete behavior: Calling delete on an array element (e.g., delete arr[1]) does not change the array length. It simply resets that specific element to its default value (0).
 * Member .push(): Only available for dynamic arrays in storage. It returns a reference to the new element.

## 3. Global Variables & The Environment

Global variables provide a window into the state of the blockchain at the moment the transaction is mined.
 * msg.data (bytes): The complete "calldata" (the raw input sent to the contract).
 * msg.sig (bytes4): The first four bytes of the calldata (the function selector).
 * tx.gasprice: The gas price of the current transaction.
 * block.chainid: A unique ID for the specific network (e.g., 1 for Ethereum Mainnet, 137 for Polygon). This is vital for preventing "Replay Attacks" across different chains.

## 4. Visibility & Access (Technical Details)

| Modifier | Contract Itself | Derived Contracts | Outside World |
|---|---|---|---|
| Private | Yes | No | No |
| Internal | Yes | Yes | No |
| Public | Yes | Yes | Yes (via Getter) |
| External | No* | No | Yes |
| *External functions can be called internally using this.functionName(), but this triggers a real message call, which is very expensive. |  |  |  |

## 5. Constant vs. Immutable (The Bytecode Difference)

Both prevent the variable from being changed, but they are stored differently:
 * constant: The value is evaluated at compile time. The compiler literally finds every instance of MY_VAR in your code and replaces it with the actual number.
 * immutable: The value is evaluated at deployment time (in the constructor). The constructor code modifies the contract's "runtime bytecode" to hardcode the value before it is saved to the blockchain.


## 6. The "Default" Values

In Solidity, there is no null or undefined. Every variable has a default "Zero State":
 * uint / int: 0
 * bool: false
 * address: 0x0000000000000000000000000000000000000000
 * enum: The first element defined in the list.
 * bytes: 0x00...
Would you like to see a practical example of how to convert between these types, such as convert
