# Building and deploying on EVM using foundry

Building and deploying an EVM (Ethereum Virtual Machine) application using the Foundry framework for smart contracts requires a slightly different CLI workflow than Hardhat, as Foundry is primarily written in Rust and prioritizes speed and low-level control.

Foundry is used for the contract development, while standard Node.js/React tools are used for the frontend integration.

Here is a comprehensive step-by-step guide showing the directory structures and CLI commands.

1. ⚙️ [Setup and Project Structure (CLI)](https://github.com/koyahness/open-source-projects/blob/main/Web3-dev-%20directory-%20structure.md)
   
1.1 Global Prerequisites

Install Rust (using rustup) and then install Foundry using foundryup.

| CLI Command | Description |
|---|---|
| `curl -L https://foundry.paradigm.xyz | bash` |
| foundryup | Installs or updates forge, cast, and anvil |

1.2 Create the Monorepo Structure

We will create a parent directory to hold both the backend (Foundry) and the frontend (React).

| Path | Description |
|---|---|
| my-foundry-dapp/ | Root Project Directory |
| my-foundry-dapp/contracts/ | Foundry project for contracts/deployment |
| my-foundry-dapp/frontend/ | React/Next.js project for the UI |

# 1. Create the root directory

```
mkdir my-foundry-dapp

cd my-foundry-dapp
```

# 2. Setup the backend (Foundry)

## This creates a standard Foundry layout (src, test, script dirs)

```

forge init contracts

cd contracts
```

# Remove the default Counter contract for a fresh start

```
rm src/Counter.sol

rm test/Counter.t.sol

cd .. # Go back to my-foundry-dapp
```

# 3. Setup the frontend (React example)
npx create-react-app frontend
cd frontend
npm install ethers # Library to interact with the blockchain
cd .. # Go back to my-foundry-dapp

2. 📝 Backend (Smart Contract) CLI Steps
We focus on the my-foundry-dapp/contracts/ directory, which now contains the default Foundry structure:
my-foundry-dapp/contracts/
├── src/              # Your Solidity contract files live here
├── script/           # Deployment scripts (Solidity/Vyper)
├── test/             # Test files (Solidity/Vyper)
├── foundry.toml      # Configuration file
└── lib/              # Dependencies (e.g., forge-std, openzeppelin)

2.1 Write, Compile, and Test the Contract
You write your contract logic inside contracts/src/MyContract.sol.
| CLI Command | Directory | Purpose |
|---|---|---|
| forge build | contracts/ | Compiles .sol files into Bytecode and ABI (stored in out/) |
| forge test | contracts/ | Runs all tests in test/ (written in Solidity) |
2.2 Configure and Write Deployment Script
Unlike Hardhat, Foundry deployment scripts are usually written in Solidity in the script/ folder.
contracts/script/Deploy.s.sol (Simplified Snippet):
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.18;
import {Script} from "forge-std/Script.sol";
import {MyContract} from "../src/MyContract.sol";

contract DeployScript is Script {
    function run() public returns (address) {
        vm.startBroadcast();
        
        MyContract myContract = new MyContract(); // Deploy the contract
        
        vm.stopBroadcast();
        return address(myContract);
    }
}

2.3 Secure Key Management and Execution (CLI)
Foundry typically relies on standard environment variables or explicit file paths for private keys. We will use environment variables, setting them temporarily in the command line for security.
| CLI Command | Directory | Purpose |
|---|---|---|
| export PRIVATE_KEY="0x..." | my-foundry-dapp/ | CRITICAL: Set the private key securely (use a different key than your main wallet). |
| export SEPOLIA_RPC="https://..." | my-foundry-dapp/ | Set the RPC URL for the target network (e.g., Sepolia). |
| source .env (if using a file) | my-foundry-dapp/ | Loads environment variables. |
| CLI Command | Directory | Purpose |
|---|---|---|
| forge script script/Deploy.s.sol --rpc-url $SEPOLIA_RPC --private-key $PRIVATE_KEY --broadcast -vvvv | contracts/ | Deploys the contract to Sepolia. Logs the Contract Address and transaction hash. |
> Key Output: The CLI output (under [Deployment] section) will provide the Contract Address, which is required for the frontend.
> 
3. 🌐 Frontend Integration
The frontend needs the Contract Address and the ABI to interact with the deployed contract.
3.1 Copy Artifacts (ABI)
The contract ABI is located in the out/ folder created by forge build.
# From the root directory: my-foundry-dapp/
cp contracts/out/MyContract.sol/MyContract.json frontend/src/MyContract.json

3.2 Implement Web3 Interaction
Use the Ethers.js library (installed in Step 1.2) to create a contract instance using the ABI and the deployed address.
// Example Frontend JS (e.g., App.js)
import abiJson from './MyContract.json'; // The copied ABI file
import { ethers } from 'ethers';

const CONTRACT_ADDRESS = "0x...Your...Deployed...Address..."; // From step 2.3
const contractABI = abiJson.abi; 

// ... (Wallet connection logic using ethers.BrowserProvider and getSigner) ...

// Create the contract instance
const contract = new ethers.Contract(CONTRACT_ADDRESS, contractABI, signer); 

// ... Interaction logic (contract.callFunction()) ...

3.3 Run and Deploy Frontend
| CLI Command | Directory | Purpose |
|---|---|---|
| npm start | frontend/ | Starts the local development server to test the DApp |
| npm run build | frontend/ | Creates the production-ready static assets |
| netlify deploy --prod --dir=build | frontend/ | Deploys static assets to a hosting service (e.g., Netlify CLI) |
4. 🧪 Local Testing with Anvil (Foundry CLI)
For rapid local testing before deploying to a public testnet, use Anvil, Foundry's fast local EVM node.
| CLI Command | Directory | Purpose |
|---|---|---|
| anvil | contracts/ | Starts a local EVM on http://127.0.0.1:8545 (default). Keep this terminal running. |
Once Anvil is running, follow these steps:
 * Configure MetaMask to point to Localhost:8545 (Chain ID: 31337).
 * Import one of the test private keys logged by the anvil command.
 * Deploy your contract to the local Anvil chain (Terminal 2):
   * forge script script/Deploy.s.sol --rpc-url http://127.0.0.1:8545 --private-key YOUR_ANVIL_PRIVATE_KEY --broadcast -vvvv
 * Run the frontend (npm start in Terminal 3) and connect the wallet to the Localhost network to test the full flow.
Would you like a more detailed explanation of how the Solidity deployment script (Deploy.s.sol) works compared to the JavaScript deployment script used in Hardhat?
