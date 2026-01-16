# Next.js App

Building a full-stack Web3 dApp using Next.js (with the App Router) and an EVM chain requires a clean separation between the smart contract logic and the Next.js application.

The most effective structure often involves a monorepo-like setup where your Next.js frontend and your Hardhat/Foundry smart contract project reside side-by-side.

## 1. 📂 Monorepo-Style Directory Structure

A recommended structure separates the blockchain logic from the application logic.

```
my-fullstack-dapp/
├── **contracts/** <-- Smart Contract Project (Hardhat/Foundry)
│   ├── contracts/             # Your Solidity files (*.sol)
│   ├── scripts/               # Deployment/interaction scripts
│   ├── test/                  # Contract tests
│   └── hardhat.config.js
├── **dapp/** <-- Next.js Application (App Router)
│   ├── app/                   # Next.js Routes & Layouts
│   │   ├── (main)/            # Route Group for primary pages
│   │   │   ├── layout.tsx
│   │   │   ├── page.tsx       # Home page (e.g., wallet connect)
│   │   │   └── dashboard/     # Protected routes
│   │   │       └── page.tsx
│   │   ├── providers.tsx      # Client Component wrapping WAGMI/RainbowKit
│   │   └── layout.tsx         # Root layout
│   ├── public/                # Static assets (images, favicon)
│   ├── src/
│   │   ├── components/        # Reusable UI components (buttons, headers)
│   │   ├── **web3/** # Web3-specific configurations and types
│   │   │   ├── config.ts      # Wagmi/Viem configuration (chains, connectors)
│   │   │   ├── abi/           # Copied/generated Contract ABIs (*.json)
│   │   │   └── hooks/         # Custom Web3 hooks (e.g., useNFTBalance)
│   │   ├── **lib/** # Utility files & Off-chain logic
│   │   │   ├── utils.ts       # General utilities
│   │   │   └── actions.ts     # Next.js Server Actions (for off-chain database)
│   │   └── styles/
│   ├── .env.local             # WalletConnect Project ID, Alchemy/Infura RPC keys
│   └── package.json
└── package.json               # Root package.json (optional, for yarn/npm workspaces)
```

## 3. 📝 Step-by-Step Guide
   
A. Smart Contract Development (contracts/)

 * ***Initialize Contracts Project***: Use Hardhat or Foundry inside the contracts/ directory.
   ```
   npx hardhat
   ```

 * ***Write and Test***: Develop your Solidity code (e.g., an ERC-20 or NFT contract) and write comprehensive tests.
 * ***Compile & Deploy***: Compile the contracts and write deployment scripts.
   ```
   npx hardhat compile
   npx hardhat run scripts/deploy.js --network sepolia
   ```

 * ***Copy Artifacts***: After deployment, copy the contract's ABI and Address to your Next.js project so the frontend can interact with it.
   * ***ABI***: Copy the generated JSON ABI file into dapp/src/web3/abi/.
   * ***Address***: Store the deployed address in a configuration file like dapp/src/web3/config.ts or an environment variable.
   
B. Next.js Frontend Setup (dapp/)
 * ***Initialize Next.js***: Create the Next.js app inside the dapp/ directory.
```unix
npx create-next-app@latest dapp --ts --app
cd dapp
```

 * Install Web3 Dependencies:
   ```
   npm install wagmi viem @rainbow-me/rainbowkit @tanstack/react-query
   ```

 * Configure Wagmi/Viem (dapp/src/web3/config.ts): Define the chains and providers for your dApp. You must obtain a WalletConnect Project ID and set it in your .env.local file.

```
   // dapp/src/web3/config.ts
import { createConfig, http } from 'wagmi';
import { mainnet, sepolia } from 'wagmi/chains';
// ... define connectors and transports
```

 * Create Web3 Provider Wrapper (dapp/app/providers.tsx): This is a Client Component that wraps your application with the necessary context for Wagmi and RainbowKit.

```
   // dapp/app/providers.tsx - MUST include 'use client'
'use client'; 
import { WagmiProvider } from 'wagmi';
import { RainbowKitProvider } from '@rainbow-me/rainbowkit';
// ... QueryClientProvider and imports

export function Providers({ children }) {
  // ... return providers (WagmiProvider, QueryClientProvider, RainbowKitProvider)
  return (/* ... */)
}
```
 * Integrate Providers: Include providers.tsx in your root dapp/app/layout.tsx.

C. Component Interaction
 * Wallet Connect: Place the <ConnectButton /> from RainbowKit in a Client Component (e.g., dapp/src/components/ConnectWallet.tsx).
 * Read Contract Data: Use the useReadContract Wagmi hook in any Client Component to fetch contract data.
 * Write Transactions: Use useWriteContract and useWaitForTransactionReceipt to handle sending transactions (e.g., minting, swapping).
This video provides a step-by-step tutorial for building a complete Ethereum dApp with Next.js, Wagmi, and RainbowKit, offering a practical demonstration of this directory structure and workflow. Build an Ethereum DApp with Next.js, Wagmi, RainbowKit, and Pinata to Mint NFTs on IPFS.
