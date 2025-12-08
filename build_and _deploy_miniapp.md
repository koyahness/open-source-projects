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
 * 
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
