# Directory structure

The front end and back end (smart contracts) in Web3 development do not need to be in the same root directory, though it is a very common and often recommended practice, especially for smaller projects.


The choice of whether to put them in the same directory (a monorepo structure) or separate directories depends on development preferences, team size, and project complexity.


Here is a breakdown of why they are separate components and the trade-offs of the directory structure:


## 🌐 Components are Logically Separate

In Web3, the "back end" is primarily the Smart Contract deployed to an EVM-compatible blockchain. The front end is a standard web application (React, Vue, etc.).


 * Smart Contract (The "Back End"): This code is compiled into bytecode and lives permanently on the decentralized network. It is handled by tools like Hardhat or Truffle.
   
 * Front End (The UI): This code is HTML, CSS, and JavaScript, hosted on a traditional web server (or decentralized storage like IPFS). It uses libraries like Ethers.js or Wagmi to send messages (transactions) to the deployed contract.
   
Because they are two distinct deployment targets, they can live independently on your local machine.

## 📂 Directory Structure Options

1. Monorepo (Same Root Directory) - Recommended for DApps
   
This is the structure used in the previous guide, where a single parent directory holds both projects.

| Path | Component | Tools |
|---|---|---|
| my-dapp/ | Root Directory | Git, Package Manager |
| my-dapp/backend/ | Smart Contracts | Hardhat, Solidity |
| my-dapp/frontend/ | Web UI | React, Ethers.js |

Pros (Why it's Common):

 * Easy Artifact Management: It's simpler to copy the compiled ABI (Contract Interface) from backend/ to frontend/ using simple file paths or CLI commands.
 * Centralized Configuration: All configurations (like network settings, deployment scripts) are in one place.
 * Simplified Tooling: One package.json file in the root (if using tools like Lerna or Yarn Workspaces) can manage dependencies for both.
   
2. Polyrepo (Separate Root Directories) - For Large Organizations
The projects are entirely separate Git repositories and codebases.

| Path | Component | Tools |
|---|---|---|
| git@github.com:myteam/smart-contracts.git | Smart Contracts | Hardhat, Solidity |
| git@github.com:myteam/web-app.git | Web UI | React, TypeScript |


Pros (When to Use):

 * Clear Separation of Concerns: Better for large teams where the smart contract team and the front end team have different release cycles and access permissions.
 * Independent Release Cycles: Contracts can be audited and deployed without waiting for the frontend features, and vice-versa.

## 💡 Key Takeaway

For most individual developers or small teams building a single DApp, the monorepo structure is highly recommended because it simplifies development, deployment, and, most importantly, the critical task of sharing the Contract Address and ABI between the two parts.

## ether.js and hardhat

Understanding how the tools enable separation in Web3 comes down to two key technical artifacts: the Contract Address and the ABI (Application Binary Interface).


Here is an explanation of how Hardhat and Ethers.js leverage these artifacts to allow the frontend and backend to remain logically separate.

🌉 The Communication Bridge: Address and ABI

The Hardhat backend and the Ethers.js frontend do not directly talk to each other; they both talk to the blockchain.

The two pieces of information they exchange are the only things needed to establish that communication:


1. The Contract Address (The Location)
   
 * Role: This is the unique, 40-character hexadecimal identifier (e.g., 0x...) that tells the entire EVM network exactly where the contract's code and storage are located on the blockchain.

 * Hardhat's Role: During deployment (npx hardhat run scripts/deploy.js), Hardhat sends the contract bytecode to the network. The network responds with the Contract Address, which Hardhat then logs in the console.
 * 
 * Frontend's Role: The front end simply treats this address as a constant variable (const CONTRACT_ADDRESS = "0x..."). It tells Ethers.js: "Look for the smart contract at this specific location."
   
2. The ABI (The Language)
 * Role: The ABI is a JSON array that acts as a blueprint or interface definition. It defines the exact names, parameter types, and return types of every public function and variable in the smart contract.
   
 * Hardhat's Role: Hardhat generates the ABI during the compilation step (npx hardhat compile). It's part of the artifact JSON file (e.g., MyContract.json).
 * 
 * Frontend's Role: Ethers.js needs the ABI to correctly package data when sending a transaction.
   
   * Example: If your Solidity function is function setValue(uint256 _newValue), the ABI tells Ethers.js:
     * The function name is setValue.
     * It requires one argument, which must be a uint256.
     * The necessary data must be encoded in a specific way (using calldata and the function selector) before being sent to the EVM.

🛠️ How Ethers.js Uses These Artifacts

Ethers.js (or similar libraries like Viem/Web3.js) is the intermediary that makes this separation possible.

 * Wallet Connection: Ethers.js first connects to the user's wallet provider (MetaMask, etc.) via the browser's window.ethereum object. This gives it access to the current network and the user's signing authority.
   
 * Contract Object Creation: The core operation in the frontend is creating a contract object:
   const contract = new ethers.Contract(CONTRACT_ADDRESS, contractABI, signer);

   * This single line of code is what bridges the gap. The contract object now has methods like contract.setValue(100) that directly mirror your Solidity functions, even though the actual code is hundreds of miles away on a decentralized server.
     
 * Sending Transactions: When you call contract.setValue(100), Ethers.js uses the ABI to:
   * Find the encoded function selector for setValue.
   * Encode the value 100 into the required EVM bytecode format.
   * Package this encoded data into a transaction targeted at the CONTRACT_ADDRESS.
   * Ask the user's wallet (signer) to sign and broadcast the transaction to the network.
     
In summary, the backend (Hardhat) produces the Address and ABI. The frontend (Ethers.js) consumes these artifacts to know where to look and how to speak to the decentralized contract, allowing both to operate independently.
