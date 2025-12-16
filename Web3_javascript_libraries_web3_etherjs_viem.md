# EVM javascript libraries

Ether.js, web3.js and viem; these three are the most prominent JavaScript libraries for interacting with the Ethereum Virtual Machine (EVM) ecosystem.

They all serve the fundamental purpose of allowing your application (DApp) to read blockchain data and send transactions using the JSON-RPC protocol. However, they differ significantly in design philosophy, modernity, and developer experience.
Here is a comparison of web3.js, ethers.js, and viem:

⚖️ Key Comparison Table

| Feature | web3.js | ethers.js | viem |
|---|---|---|---|
| Design Philosophy | Monolithic, feature-rich | Modular, focus on wallet/provider separation | Minimalist, unopinionated, lightweight, utility-focused |
| Maturity | Oldest (Established by Ethereum Foundation) | Highly mature (Most popular choice for a long time) | Newest (Gaining rapid adoption) |
| Bundle Size | Largest (Can be bulky for frontend) | Smaller, modular | Smallest (Optimized for performance) |
| Language | JavaScript (TypeScript support added later) | Full TypeScript support | Full TypeScript support |
| Async Handling | Mostly Callbacks and Promises | Exclusively Promises (modern async/await friendly) | Exclusively Promises (modern async/await friendly) |
| Big Number | Converts to JavaScript Number (potential precision loss) | Custom BigNumber implementation | Native BigInt (modern, best precision) |
| Core Abstraction | Node connection and key management often bundled. | Explicit separation of Provider (node) and Wallet (signer). | Separation of Client (Public, Wallet, Test) and other utilities. |
| Contract Factory | Requires more manual setup for deployment. | Simplified with built-in ContractFactory. | Highly simplified and explicit contract interaction. |
| Ecosystem | Large, but sometimes considered legacy/complex. | Large, robust, and well-documented. | Smaller but rapidly growing, often paired with Wagmi hooks. |

## 💡 Detailed Breakdown

1. web3.js
web3.js is the original JavaScript library for Ethereum.
 * Pros:
   * Historical Footprint: Longest track record and largest existing community/resources.
   * Comprehensive: It's a "Swiss Army knife" with modules for various aspects of the Ethereum ecosystem (like Whisper and Swarm in older versions).
 * Cons:
   * Monolithic/Size: It's a larger library, which can lead to increased bundle size for frontend applications.
   * API Design: Traditionally relied on callbacks, making for less modern-looking, nested code (though Promises have been introduced).
   * Big Number Issues: Handling large integers (like token balances) can lead to precision errors as it defaults to standard JavaScript numbers.
2. ethers.js
ethers.js emerged as a modern, lightweight, and user-friendly alternative to web3.js.
 * Pros:
   * Wallet/Provider Separation: Its fundamental design clearly separates the read-only connection to the blockchain (Provider) from the key management and transaction signing (Wallet), which is a much cleaner architecture.
   * TypeScript: Built with TypeScript from the start, offering excellent type safety and code hints.
   * Modern API: Uses Promises exclusively, leading to clean async/await syntax.
   * Lightweight: Much smaller than web3.js, making it ideal for performance-sensitive DApps.
 * Cons:
   * Custom Big Number: While safer than web3.js, it uses its own custom BigNumber class, which sometimes requires extra conversions to interact with other libraries that use native JavaScript BigInt.
3. viem
viem is the new and innovative contender, developed by the team behind the popular Wagmi React hooks library.
 * Pros:
   * Minimalist & Performant: Extremely lightweight and unopinionated, focusing on being a bare-bones Ethereum client interface.
   * Native BigInt: Uses native JavaScript BigInt for large numbers, which is the modern standard, simplifying integration with other JavaScript packages.
   * Modular and Type-Safe: Fully type-safe and highly modular. You only import exactly what you need.
   * Developer Experience: Offers a clean, modern API that feels very "native" to contemporary JavaScript/TypeScript development.
   * Ecosystem Integration: Works seamlessly with Wagmi (for React/frontend development) and Viem's own utilities.
 * Cons:
   * Maturity: Being the newest, its community and documentation are still growing compared to the other two, though adoption is very fast.
📝 Conclusion and Recommendation
The "best" choice depends on your project:
 * For a Legacy or Highly Established DApp: You might stick with web3.js if your existing codebase already relies on it.
 * For the Modern Standard: ethers.js is the time-tested, secure, and popular choice with excellent documentation and a massive community. It's a safe default for almost any project.
 * For Performance-Critical, New, or React Projects: viem is the top-tier modern choice. It's the lightest, uses the newest standards (like BigInt), and pairs perfectly with the Wagmi ecosystem. It is often recommended as the new go-to for modern frontend development.
Would you like to see a code example comparing how they might perform a common task, like getting an account's balance?

#Practial differences

That's a great way to see the practical differences!
Here is a comparison of how you would perform the very common task of getting the ETH balance of an address using all three libraries.
We will use the same public test address, for example: 0xd8dA6BF26964aF9D7eEd9e03E53415D37aA96045 (Vitalik Buterin's ENS address).
💻 Code Example: Getting an Account Balance
The key differences to look for are:
 * Instantiation: How you connect to the node (RPC Provider).
 * API Naming: The function names and argument format.
 * Result Type: What kind of value the function returns for the balance.
| Library | Code Snippet (Connecting to a Public Node) | Result Type |
|---|---|---|
| web3.js | javascript<br>const Web3 = require('web3');<br>const provider = 'https://eth-mainnet.public.blastapi.io';<br>const web3 = new Web3(provider);<br><br>web3.eth.getBalance('<ADDRESS>').then(balance => {<br>  // 'balance' is a string in **wei**<br>  console.log(web3.utils.fromWei(balance, 'ether'));<br>});<br> | String (in wei) |
| ethers.js | javascript<br>const { ethers } = require('ethers');<br>const provider = new ethers.JsonRpcProvider('https://eth-mainnet.public.blastapi.io');<br><br>provider.getBalance('<ADDRESS>').then(balance => {<br>  // 'balance' is a **BigNumber** object<br>  console.log(ethers.formatEther(balance));<br>});<br> | BigNumber object (in wei) |
| viem | javascript<br>import { createPublicClient, http } from 'viem';<br>import { mainnet } from 'viem/chains';<br><br>const client = createPublicClient({<br>  chain: mainnet,<br>  transport: http('https://eth-mainnet.public.blastapi.io'),<br>});<br><br>client.getBalance({<br>  address: '<ADDRESS>',<br>}).then(balance => {<br>  // 'balance' is a native **BigInt** (in **wei**)<br>  console.log(balance); // Need utility to format to ETH<br>});<br> | Native BigInt (in wei) |
🚀 Key Takeaways from the Example
1. Instantiation
 * web3.js: Very simple, just passes the URL directly to the Web3 constructor.
 * ethers.js: Explicitly uses JsonRpcProvider for connection.
 * viem: Uses a more structured, functional approach: createPublicClient with an explicit chain (for types) and transport (for the connection). This is common in modern JavaScript utility libraries.
2. API Design
 * web3.js: Uses nested properties (web3.eth.getBalance).
 * ethers.js: Uses a direct method on the Provider object (provider.getBalance).
 * viem: Uses a functional/object-based argument style (client.getBalance({ address: ... })), which is highly type-safe and easier to work with in TypeScript.
3. Handling Large Numbers (The Most Important Difference)
This is where the distinction is clearest:
 * web3.js: Returns the balance as a string in wei (the smallest unit, 10^{-18} ETH). You must use a utility (web3.utils.fromWei) to format it for display.
 * ethers.js: Returns its custom BigNumber object. This is safer than a native JavaScript number but requires its own formatting utility (ethers.formatEther).
 * viem: Returns the modern, native JavaScript BigInt. While you still need a utility to format it to a human-readable ETH value, the underlying data type is the official JavaScript standard for integers larger than 2^{53}-1.
Do you have any specific task or project in mind (e.g., deploying a contract, interacting with an ERC-20 token) where you'd like to see another comparison?
