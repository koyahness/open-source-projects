# Building a calculator mini-app on Base

Building a calculator mini-app on Base involves two main components: the standard frontend calculator logic (HTML/CSS/JS or React) and the Base/Farcaster integration using the MiniKit template. Since a calculator's logic is typically off-chain, the focus here is on setting up the correct Base Mini App environment.


Here is a step-by-step guide with the necessary code steps to get the project running.

## 🚀 Step 1: Scaffold the Base Mini App Project

The fastest way to start is by using the official Base/OnchainKit Mini App template, which provides all the necessary boilerplate for Base and Farcaster integration (built on Next.js/React).

Code Step 1.1: Create Project

Open your terminal and run the following command to bootstrap your new Mini App:

```
npx create-onchain@latest --mini
```

When prompted, name your project (e.g., base-calculator-mini-app) and select your preferred language/package manager (TypeScript/pnpm is common).

Code Step 1.2: Install Dependencies

Navigate into your new project directory and install the necessary packages.

```
cd base-calculator-mini-app
```

```
pnpm install # or npm install or yarn install
```

## 💻 Step 2: Build the Calculator UI and Logic

Since this is a simple calculator with no direct onchain transactions (like token transfers), the logic will be pure frontend/client-side JavaScript.

We will replace the starter template's home page with our calculator component.

### Code Step 2.1: Create the Calculator Component

Create a new file, for example,

components/Calculator.tsx, and add the basic structure and logic. This example uses a simplified state management approach common in React.

```
// components/Calculator.tsx
import React, { useState } from 'react';
import { useMiniKit } from '@coinbase/onchainkit/minikit';

export function Calculator() {
  const { context } = useMiniKit();
  const [display, setDisplay] = useState('0');
  const [operator, setOperator] = useState<string | null>(null);
  const [firstOperand, setFirstOperand] = useState<number | null>(null);
  const [waitingForSecondOperand, setWaitingForSecondOperand] = useState(false);

  const clear = () => {
    setDisplay('0');
    setOperator(null);
    setFirstOperand(null);
    setWaitingForSecondOperand(false);
  };

  const inputDigit = (digit: string) => {
    if (waitingForSecondOperand) {
      setDisplay(digit);
      setWaitingForSecondOperand(false);
    } else {
      setDisplay(display === '0' ? digit : display + digit);
    }
  };
  
  // (Note: The full calculation logic is omitted for brevity but would go here)

  return (
    <div className="p-4 rounded-lg bg-gray-900 shadow-xl">
      <input
        type="text"
        className="w-full h-16 mb-4 text-4xl text-right bg-gray-700 text-white rounded p-2"
        value={display}
        readOnly
      />
      <div className="grid grid-cols-4 gap-2">
        <button className="bg-red-500 hover:bg-red-600 p-4 rounded text-xl" onClick={clear}>C</button>
        {/* ... Calculator Buttons (0-9, +, -, *, /) ... */}
        <button className="bg-blue-500 hover:bg-blue-600 p-4 rounded text-xl" onClick={() => inputDigit('1')}>1</button>
        <button className="bg-blue-500 hover:bg-blue-600 p-4 rounded text-xl" onClick={() => inputDigit('2')}>2</button>
        {/* ... */}
      </div>
      <p className="mt-4 text-sm text-gray-400">
        Base App Context FID: {context?.fid}
      </p>
    </div>
  );
}
```

### Code Step 2.2: Integrate the Component

Replace the content of your main app file (usually app/page.tsx or similar) with the new component.

// app/page.tsx (simplified example)

import { Calculator } from '@/components/Calculator';

export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center p-4 bg-gray-800">
      <h1 className="text-white text-3xl mb-6">Base Calculator Mini App</h1>
      <Calculator />
    </main>
  );
}

## 🌐 Step 3: Deploy and Get the Farcaster Manifest

A Mini App must be accessible via a public URL and have a valid Farcaster manifest to be recognized by the Base App.

### Code Step 3.1: Run Locally for Development
Start your development server to test the calculator in your browser. You may need a tunneling tool like ngrok to test it inside the Base dev app environment.
pnpm run dev

### Code Step 3.2: Deploy to Production (Vercel)

For the final deployment, hosting providers like Vercel are recommended.
 * Commit your code to a GitHub repository.
 * Import the repository into Vercel and deploy it.
 * Once deployed, you will get your public URL (e.g., https://base-calculator.vercel.app).
   
### Code Step 3.3: Verify the Farcaster Manifest

The template you used already includes the Farcaster manifest file, usually located at public/.well-known/farcaster.json. You simply need to ensure your production URL is set as an environment variable (often NEXT_PUBLIC_URL) in your hosting provider's settings.
The Base App will use this manifest to verify and load your calculator Mini App.
This video shows the quick-start process for setting up a Base Mini App using MiniKit, which is the foundational step for deploying your calculator.
10-min MiniKit Quickstart: zero to mini app on @base

YouTube video views will be stored in your YouTube History, and your data will be stored and used by YouTube according to its Terms of Service

https://www.youtube.com/live/IFOSDhsEbZQ?si=qXwknON0Iqldzg3P

# Integrate the onchain component into your mini-app!

To build a calculator mini-app on Base that mints an NFT upon score submission, you need to combine the frontend logic with a backend Smart Contract and use Base's OnchainKit for transaction handling.

## Here is the step-by-step guide with the necessary code components.

### Part 1: Smart Contract Setup (Onchain)

You need a simple contract that allows anyone to mint an NFT and record the score (an integer) associated with that mint. We will use a basic ERC-721 contract structure.

1. The Smart Contract (Solidity)
   
Create a contract that stores the score as metadata or simply as a mapping (for a true NFT implementation, you would use a metadata URI, but we'll simplify by just storing the score for this tutorial).
File: contracts/ScoreNFT.sol

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.0;

import "@openzeppelin/contracts/token/ERC721/ERC721.sol";
import "@openzeppelin/contracts/access/Ownable.sol";
import "@openzeppelin/contracts/utils/Counters.sol";

contract ScoreNFT is ERC721, Ownable {
    using Counters for Counters.Counter;
    Counters.Counter private _tokenIdCounter;

    // A mapping to store the score for each token ID
    mapping(uint256 => uint256) public tokenScores;

    constructor() ERC721("HackathonScoreNFT", "HACK") Ownable(msg.sender) {}

    // Function to be called from the Mini App
    function submitScore(uint256 _score) public {
        // 1. Increment Token ID
        _tokenIdCounter.increment();
        uint256 newItemId = _tokenIdCounter.current();
        
        // 2. Mint the NFT to the caller (msg.sender)
        _safeMint(msg.sender, newItemId);

        // 3. Record the score
        tokenScores[newItemId] = _score;
        
        // You could add an event here to track scores off-chain
        // emit ScoreSubmitted(msg.sender, newItemId, _score);
    }
}
```

2. Deployment
   
 * Tooling: Use a development environment like Hardhat or Foundry to compile and deploy your contract.
 * Network: Deploy the ScoreNFT contract to the Base Sepolia Testnet.
 * Result: Once deployed, you will receive the Contract Address (e.g., 0xAbc...123) and the Contract ABI (a JSON file). You will need these two things for the next part.


### Part 2: Frontend Mini App Integration

We will modify the Calculator.tsx component from the previous guide to use the calculated result to trigger a transaction using OnchainKit.

3. Define Contract Details in the Frontend
Create a file to store your contract's address and the necessary function interface (ABI snippet).
File: lib/contract.ts
import { Address } from 'viem';

// ⚠️ REPLACE this address with your deployed contract address on Base Sepolia
export const SCORE_NFT_ADDRESS: Address = '0x123...abc'; 

// Minimal ABI required for the submitScore function
export const SCORE_NFT_ABI = [
  {
    inputs: [{ name: '_score', type: 'uint256' }],
    name: 'submitScore',
    outputs: [],
    stateMutability: 'nonpayable',
    type: 'function',
  },
] as const;

4. Update the Calculator Component
We replace the simple "equals" functionality with a button that triggers the onchain minting process using the TransactionButton component from OnchainKit.
File: components/Calculator.tsx (Modified)
import React, { useState } from 'react';
import { TransactionButton, type TransactionCall } from '@coinbase/onchainkit/transaction';
import { SCORE_NFT_ADDRESS, SCORE_NFT_ABI } from '@/lib/contract';
import { baseSepolia } from 'wagmi/chains';

export function Calculator() {
  const [display, setDisplay] = useState('0');
  const [scoreToSubmit, setScoreToSubmit] = useState<number | null>(null);

  // Simplified calc function (assumes a simple integer result)
  const calculate = () => {
    try {
      // NOTE: Using 'eval' is dangerous in production code. 
      // Replace with a safer arithmetic parser for a real app.
      const result = Math.floor(eval(display)); 
      setDisplay(String(result));
      setScoreToSubmit(result); // Set the score to be minted
    } catch {
      setDisplay('Error');
      setScoreToSubmit(null);
    }
  };

  const clear = () => {
    setDisplay('0');
    setScoreToSubmit(null);
    // Reset other state like operator/operands if needed
  };

  // Function to create the transaction call for OnchainKit
  const createMintCall = (score: number): TransactionCall => {
    return {
      to: SCORE_NFT_ADDRESS,
      chain: baseSepolia,
      data: {
        abi: SCORE_NFT_ABI,
        functionName: 'submitScore',
        args: [BigInt(score)], // Pass the score as a BigInt/uint256
      },
    };
  };

  // --- UI and Button Renders ---

  return (
    <div className="p-4 rounded-lg bg-gray-900 shadow-xl max-w-sm">
      <input
        type="text"
        className="w-full h-16 mb-4 text-4xl text-right bg-gray-700 text-white rounded p-2"
        value={display}
        readOnly
      />
      
      {/* ... Calculator keys (0-9, +, -, *, /) go here ... */}

      <div className="grid grid-cols-4 gap-2 mt-4">
        {/* The EQUALS button triggers the calculation and sets the score */}
        <button className="bg-green-500 hover:bg-green-600 col-span-2 p-4 rounded text-xl" onClick={calculate}>
          = Calculate Score
        </button>

        {/* The MINT button is the onchain interaction */}
        {scoreToSubmit !== null && display !== 'Error' ? (
          <TransactionButton
            className="col-span-2 p-4 rounded text-xl bg-purple-600 hover:bg-purple-700 text-white disabled:opacity-50"
            calls={[createMintCall(scoreToSubmit)]}
            // Optional: Provide feedback to the user
            onSuccess={() => alert(`NFT minted! TokenID: ${scoreToSubmit}`)}
            onError={(e) => alert(`Minting failed: ${e.message}`)}
          >
            Mint Score NFT ({scoreToSubmit})
          </TransactionButton>
        ) : (
          <button className="col-span-2 p-4 rounded text-xl bg-gray-600 text-gray-400 cursor-not-allowed" disabled>
            Calculate to Mint
          </button>
        )}
      </div>
      <button className="w-full mt-4 bg-red-500 hover:bg-red-600 p-2 rounded text-lg" onClick={clear}>C (Clear)</button>
    </div>
  );
}

Part 3: Final Steps and Testing
 * Environment Setup: Ensure your Next.js/React app is wrapped with MiniKitProvider and WagmiConfig (the starter template already handles this).
 * Testing:
   * Run your app locally (pnpm run dev).
   * Perform a calculation (e.g., 2 * 3 = 6).
   * Click the Mint Score NFT (6) button. This will open the connected Base Wallet (or other Base-compatible wallet) and prompt the user to sign and submit the transaction to the Base Sepolia network.
 * Deployment: Deploy your finished Mini App to a public hosting provider like Vercel and ensure your Farcaster Manifest is configured as described in the previous response.
