# EVM development frameworks

These three frameworks—Truffle, Hardhat, and Foundry—represent the dominant, but distinct, approaches to professional EVM (Ethereum Virtual Machine) development today.


Here is a comprehensive comparison focusing on their core philosophies, key features, language focus, and deployment workflows.

## ⚔️ Truffle vs. Hardhat vs. Foundry: The EVM Framework Showdown

| Feature | Truffle Suite | Hardhat | Foundry |
|---|---|---|---|
| Core Philosophy | Full-stack DApp development: focuses on migrations, testing, and UI integration. | Flexible, extensible environment: focuses on local development and debugging. | EVM-native development: focuses on speed, low-level control, and writing logic in Solidity. |
| Primary Language | JavaScript/Node.js | JavaScript/TypeScript | Solidity/Rust |
| Local Node | Ganache CLI (Separate tool) | Hardhat Network (Built-in) | Anvil (Built-in) |
| Deployment Scripts | JavaScript (Migrations) | JavaScript/TypeScript | Solidity (Forge Scripts) |
| Testing Language | JavaScript (Mocha/Chai) or Solidity | JavaScript/TypeScript or Solidity | Solidity (Recommended) |
| EVM Debugging | Debugger CLI (Can be slow) | Built-in stack traces and logging (console.log) | Excellent (using forge test -vvv and forge debug) |
| Artifacts | build/contracts/*.json (Includes all network addresses) | artifacts/contracts/*/*.json (Separate files) | out/*/*.json (ABI/Bytecode) |
| Ecosystem Strength | Strong community support, historical use, full suite (including Ganache and Drizzle). | Modern industry standard, highly flexible, extensive plugin system. | Fastest compilation, most powerful for writing complex tests in Solidity. |


1. 🐘 Truffle: The Pioneer (JavaScript-Centric)
   
Truffle was the original full-stack framework and is known for its "suite" approach, integrating multiple tools for a complete DApp workflow.
 * Deployment: Uses Migrations (JavaScript files) which ensure contracts are deployed in a specific, ordered manner.
 * Workflow: The reliance on JavaScript for almost everything makes it comfortable for developers coming from a web background.
 * Trade-off: Truffle's full-stack nature can sometimes feel heavier or slower than its competitors, particularly during compilation and testing.
   
2. 🧱 Hardhat: The Modern Standard (TypeScript/JS)
Hardhat is the current industry leader, offering superior local development and debugging features due to its powerful, built-in Hardhat Network and robust plugin architecture.
 * Local Development: Its built-in network provides immediate feedback and automatic Solidity stack traces when transactions fail. It popularized the console.log() usage within Solidity for debugging.
 * Deployment: Uses simple JavaScript/TypeScript scripts executed via npx hardhat run.
 * Flexibility: It has a massive ecosystem of plugins (Ethers, TypeChain, etc.), making customization easy.
   
3. 🔨 Foundry: The Performance Champion (Solidity-Native)
Foundry, written in Rust, is the newest framework and is geared towards performance, security, and low-level control.
 * Speed: It is significantly faster than Node.js-based frameworks due to its Rust core and parallel execution capabilities.
 * Testing/Scripting: It uniquely allows developers to write tests and deployment scripts entirely in Solidity (.t.sol and .s.sol files). This means developers don't have to context-switch to JavaScript.
 * Debugging/Security: The built-in Fuzzing capability (testing with massive randomized inputs) and its pre-broadcast simulation (forge script) offer high levels of confidence before deploying to a live network.
   
Conclusion: Choosing the Right Tool

The best framework depends on the developer and the project's priorities:

| Scenario | Best Framework | Reasoning |
|---|---|---|
| Web2 Developer Transitioning | Hardhat or Truffle | Familiarity with JavaScript/TypeScript and Node.js toolchains. |
| High-Security/High-Performance Core | Foundry | Fastest compilation, powerful fuzz testing, and secure, deterministic deployment simulation. |
| Large/Complex Project with Monorepo | Hardhat | Excellent plugin support and configuration flexibility for managing complex setups. |
| Historical or Educational Use | Truffle | Still widely used in tutorials and older projects; provides a clear, integrated suite. |


## Foundry and Hardhat ecosystems: the language used for scripting and deployment.

Foundry's approach, using Solidity Scripts, keeps the entire smart contract development and deployment workflow within the Solidity/EVM language. Hardhat's approach uses JavaScript/TypeScript Scripts, bridging the gap between contracts and the broader web development ecosystem.

Here is a detailed comparison of the two approaches, focusing on the CLI workflow and execution:

⚔️ Deployment Script Comparison: Foundry vs. Hardhat

| Feature | Foundry (Solidity Script) | Hardhat (JavaScript/TypeScript Script) |
|---|---|---|
| Language | Solidity (in script/Deploy.s.sol) | JavaScript/TypeScript (in scripts/deploy.js) |
| Execution Tool | forge script | npx hardhat run |
| Transaction Logic | Uses EVM Cheat Codes (vm.startBroadcast(), new MyContract(), vm.stopBroadcast()) | Uses Ethers.js/Web3.js library calls (ethers.getContractFactory(), MyContract.deploy()) |
| Local Simulation | Built-in to forge script. Runs a "dry run" simulation locally or on a fork before sending any transaction. | Not explicitly built-in; standard JS execution. Relies on the local Hardhat network for testing and simulation. |
| Gas/Transaction Fees | Managed by the forge script CLI flags (--gas-price, --legacy, etc.) | Managed by the network object and Ethers.js within the JavaScript file. |


Foundry: The Solidity-Native Workflow

The Foundry workflow is designed to be fast and deterministic by simulating the exact EVM execution before broadcasting.

1. The Script (Solidity)
   
Foundry's script is a Solidity contract (e.g., Deploy.s.sol) that inherits from forge-std/Script.sol.

```
// contracts/script/Deploy.s.sol
contract DeployScript is Script {
    function run() public {
        // 1. Start Recording Transactions
        vm.startBroadcast(); 
        
        // 2. The Deployment Call (This is what gets recorded)
        new MyContract(); 
        
        // 3. Stop Recording
        vm.stopBroadcast();
    }
}
```

2. The Execution (CLI)

| CLI Command | Directory | Purpose |
|---|---|---|
| forge script script/Deploy.s.sol --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY --broadcast -vvvv | contracts/ | Executes the script. |

The forge script Phases:

 * Local Simulation (Dry Run): forge script first runs the run() function in a local EVM simulation, using the context of the target network if an RPC URL is provided. It records every transaction that occurs between vm.startBroadcast() and vm.stopBroadcast().
 * Onchain Simulation (Optional): If a fork is used, the transactions are executed again on the forked chain to ensure correctness.
 * Broadcasting: Only if the simulation succeeds and the --broadcast flag is present, Foundry uses the provided --private-key to sign and send the recorded transactions to the target network (Sepolia, Mainnet, etc.).
   
This pre-simulation step is a major security and efficiency advantage, as you can confirm the exact transactions, gas usage, and final state change before spending real gas.


Hardhat: The JS/TS Workflow

Hardhat's workflow treats the deployment as a standard asynchronous Node.js task, using the Ethers.js library to manage blockchain interaction.

1. The Script (JavaScript)
Hardhat scripts (e.g., deploy.js) use the asynchronous nature of JavaScript to wait for transactions.

```
// scripts/deploy.js
async function main() {
    // 1. Get the Contract Factory (Ethers.js object)
    const MyContract = await hre.ethers.getContractFactory("MyContract");

    // 2. The Deployment Call (Transaction is immediately sent)
    const myContract = await MyContract.deploy();
    
    // 3. Wait for the transaction to be mined
    await myContract.waitForDeployment(); 
}
// ... error handling ...
```

3. The Execution (CLI)

| CLI Command | Directory | Purpose |
|---|---|---|
| npx hardhat run scripts/deploy.js --network sepolia | backend/ | Executes the JavaScript file as a Node.js process. |

The npx hardhat run Flow:

 * Execution: npx hardhat run executes the script. The script uses Ethers.js to access the network configuration (RPC URL and private key) defined in hardhat.config.js.
 * Immediate Broadcast: When MyContract.deploy() is called, Ethers.js immediately packages the transaction, signs it with the private key, and broadcasts it to the specified network RPC URL.
 * Synchronization: The script then waits for the network to confirm the transaction using myContract.waitForDeployment().
   
The main difference is that Hardhat relies on the reliability of the Ethers.js library to correctly format and send the transaction, while Foundry uses a Solidity environment for deterministic, pre-broadcast verification.

Visualizing the core difference in the CLI deployment process. Seeing the file content side-by-side highlights how Foundry is native to the EVM language (Solidity), while Hardhat is native to the web ecosystem (JavaScript).

Here is a side-by-side comparison of the file structure and the key code required for deployment in both frameworks:

## 📐 Deployment Script Code Comparison

| Aspect | Foundry Workflow (Solidity) | Hardhat Workflow (JavaScript) |
|---|---|---|
| Project Directory | my-foundry-dapp/contracts/ | my-hardhat-dapp/backend/ |
| File Location | script/Deploy.s.sol | scripts/deploy.js |
| Core Dependency | forge-std/Script.sol (Solidity Library) | hardhat/ethers (JavaScript Library) |
| Execution Command | forge script script/Deploy.s.sol --broadcast | npx hardhat run scripts/deploy.js |
| Execution Language | Solidity | JavaScript (Node.js) |

Foundry: script/Deploy.s.sol

This script uses Foundry's built-in EVM Cheat Codes (vm.*) to tell the EVM when to record and broadcast transactions.

```
// contracts/script/Deploy.s.sol

pragma solidity ^0.8.18;
import {Script} from "forge-std/Script.sol";
import {MyContract} from "../src/MyContract.sol";

contract DeployScript is Script {
    function run() public returns (address) {
        // --- EVM TRANSACTION RECORDING START ---
        vm.startBroadcast();
        
        // This is a direct EVM op: CREATE2 (contract deployment)
        MyContract myContract = new MyContract(); 
        
        vm.stopBroadcast();
        // --- EVM TRANSACTION RECORDING END ---

        return address(myContract); // Return the deployed address
    }
}
```

Hardhat: scripts/deploy.js

This script uses the ethers.js library to interact with the blockchain via RPC calls.

```
// backend/scripts/deploy.js

// Hardhat runtime environment (HRE) provides 'ethers'
async function main() {
    // 1. Get the Factory object from Ethers
    const MyContract = await hre.ethers.getContractFactory("MyContract");

    // 2. Deployment Call (Ethers handles ABI encoding and signing)
    const myContract = await MyContract.deploy();
    
    // 3. Wait for the transaction to be mined
    await myContract.waitForDeployment(); 

    // 4. Log the address for the frontend
    const contractAddress = await myContract.getAddress();
    console.log(`Contract deployed to: ${contractAddress}`);
}

main().catch((error) => {
    console.error(error);
    process.exit(1);
});
```

This comparison visually captures how Foundry minimizes the technological stack by keeping the deployment logic within the Solidity ecosystem, while Hardhat utilizes the vast JavaScript/Node.js ecosystem for its tooling.
