# Understanding Layer 1, Layer 2, and Rollup Scaling Solutions

[Sequencers](https://updraft.cyfrin.io/courses/blockchain-basics/scalability/centralized-sequencers)


The Ethereum network, like many foundational blockchains, faces a core challenge known as the Blockchain Trilemma.

This concept posits that a blockchain can only optimize for two of three critical properties:

**Decentralization, security, and scalability**

Ethereum was designed to prioritize decentralization and security, which has made it the most robust and trusted smart contract platform.

However, this focus comes at the cost of scalability, limiting the network to approximately 15 transactions per second. When demand is high, this limitation leads to network congestion and prohibitively expensive transaction fees, or "gas."

To solve this, developers created scaling solutions that can increase transaction throughput without sacrificing the security and decentralization of the main network.

The most prominent of these are Layer 2 solutions, particularly Rollups. This lesson breaks down these foundational concepts.

# What is a Layer 1 (L1) Blockchain?

A Layer 1, or L1, is the base protocol of a blockchain ecosystem. It is the fundamental network responsible for maintaining its own security and reaching a consensus on the state of the ledger. An L1 operates independently and does not rely on any other network for finality or security.

Because it serves as the ultimate source of truth where all transactions are eventually finalized, a Layer 1 is often referred to as the **Settlement Layer**.

All transactions, including those processed on auxiliary layers, must ultimately settle on the L1 to be considered complete and irreversible.

Examples of Layer 1 Blockchains:

* Ethereum Mainnet
* Bitcoin
* Solana
* BNB Chain

Ethereum testnets, like Sepolia, also function as L1s in their respective environments.

## What is a Layer 2 (L2) Blockchain?

A Layer 2, or L2, is a separate blockchain protocol built on top of a Layer 1. Its primary purpose is to extend the capabilities of the L1, most notably to improve scalability. An L2 processes transactions on its own chain but "hooks back into" the L1, inheriting the robust security and decentralization of its parent layer.

It is crucial to distinguish an L2 from a decentralized application (dApp). A dApp like Uniswap is a program that is deployed on an L1 blockchain. In contrast, an L2 is an entire, separate blockchain network designed to augment the L1 it is built upon.

Examples of Layer 2 Blockchains:

* ZKsync
* Optimism (OP)
* Arbitrum
* Polygon

## Rollups: The Primary L2 Scaling Solution

Rollups are the most widely adopted type of L2 scaling solution. They work by executing transactions on the L2 chain (off-chain from the L1 perspective) and then bundling, or "rolling up," hundreds of them into a single, compressed transaction batch. This single batch is then posted to the Layer 1 network.

This process dramatically increases transaction throughput and reduces costs for end-users. The gas fee required to post the single batch to the L1 is shared across all the individual transactions contained within it, making each one significantly cheaper than if it were processed directly on the L1.

There are two primary types of rollups, distinguished by how they prove the validity of their transaction batches to the L1: Optimistic Rollups and Zero-Knowledge Rollups.

## Optimistic Rollups

Optimistic Rollups operate on the principle that all transactions in a batch are valid by default—an "optimistic" assumption. After an L2 operator posts a batch to the L1, a Challenge Period begins, which typically lasts about a week.

During this window, any other network participant can scrutinize the batch. If they identify an invalid transaction, they can submit a Fraud Proof to the L1. This triggers a dispute resolution process on the L1 to verify the claim.

If the fraud proof is successful: The fraudulent batch is reverted, and the malicious operator who submitted it is penalized by having their staked collateral slashed.

If the challenge period ends without a successful challenge: The batch is considered final and is permanently recorded on the L1.

The primary trade-off of this model is the long waiting period for transaction finality. Users must wait for the challenge period to conclude before they can withdraw their funds from the L2 back to the L1.

## Zero-Knowledge (ZK) Rollups

Zero-Knowledge (ZK) Rollups take a different approach. Instead of assuming validity and waiting for challenges, they proactively prove the validity of every transaction batch using advanced cryptography.

When a ZK-Rollup operator submits a batch to the L1, they also generate and submit a cryptographic Validity Proof, specifically a Zero-Knowledge Proof (ZKP). This proof mathematically guarantees that all the state changes within the batch are correct and follow the network's rules.

A smart contract on the L1, known as the Verifier, can instantly check this proof. If the proof is valid, the batch is accepted and finalized immediately. This eliminates the need for a lengthy challenge period, allowing for much faster withdrawals and finality compared to Optimistic Rollups.

A key feature of the ZKPs used in most rollups is succinctness, meaning the proof is very small and fast to verify, even if it represents an immense amount of computation. For this reason, these are sometimes called Succinct Rollups.

While most ZK-Rollups use this technology for scaling, some also leverage the "zero-knowledge" property to provide privacy, enabling secret balances and confidential transactions. These are often called ZK-ZK Rollups, with Aztec being a prominent example.

## Conclusion

Layer 1 blockchains like Ethereum provide unmatched security and decentralization but struggle with scalability. Layer 2 solutions, and specifically Rollups, solve this problem by processing transactions on a separate, faster layer while inheriting the security of the L1. Optimistic Rollups achieve this through a fraud-proof system with a time delay, while ZK-Rollups use cryptographic validity proofs for instant finality. Together, these technologies enable the Ethereum ecosystem to scale, paving the way for mainstream adoption.

Centralized Sequencers: The Single Point of Failure in Blockchain Rollups
In the world of blockchain rollups, the sequencer is a critical component responsible for maintaining the flow and order of transactions. While essential for efficiency, the current implementation in most rollups presents a significant risk to the network and its users. This is because, in their current state, most sequencers are centralized.

A sequencer is a specialized operator whose primary job is to order transactions submitted by users. In some cases, it also performs the secondary role of bundling these ordered transactions together before they are submitted to the main blockchain. When this powerful role is controlled by a single entity, we call it a centralized sequencer. This concentration of power introduces two fundamental problems that strike at the core principles of blockchain technology: censorship and reliability.

## The Risk of Censorship
A centralized sequencer controlled by a single party has the ultimate power to decide which transactions are included in a block and in what order. This opens the door for malicious behavior and censorship.

A dishonest sequencer could simply refuse to include transactions from specific users, effectively blocking them from using the network. Imagine trying to withdraw your funds from a rollup, only to have a malicious sequencer repeatedly ignore your transaction request. In this scenario, your assets are trapped, held hostage by a single entity.

Furthermore, a centralized sequencer can manipulate the order of transactions to extract financial value for itself, a practice known as Maximal Extractable Value (MEV). By reordering transactions, it can front-run trades or take advantage of arbitrage opportunities at the expense of ordinary users.

The Problem of Reliability: A Single Point of Failure
Beyond malicious intent, centralization creates a critical vulnerability: a single point of failure. The entire rollup network depends on this one sequencer to function. If that single entity goes offline for any reason—be it a technical glitch, a targeted attack, or a regulatory shutdown—the entire rollup halts.

When the sequencer is down, no new transactions can be processed. This means no deposits, no transfers, and, most importantly, no withdrawals. The network becomes completely unusable.

Consider the implications. What happens if you can't access your funds for a day? What if the outage lasts a week? A year? What if the sequencer never comes back online? If you cannot access or move your assets, their value is effectively zero. This existential threat undermines the very promise of user-owned assets that blockchain technology is supposed to guarantee.

## The Solution: The Path to Decentralization
The clear and necessary solution to the risks of centralization is to decentralize the sequencer role. The end goal for any mature rollup is to have a system with multiple, independent sequencers who work together to order and process transactions.

In a decentralized model, if one sequencer goes offline or acts maliciously, others can step in to ensure the network remains live, operational, and fair. This redundancy eliminates the single point of failure and drastically reduces the risk of censorship, as no single party has ultimate control.

Projects in the space, such as zkSync, are actively working on this transition. Many rollups launch with a centralized sequencer for reasons of simplicity and rapid development in their early stages. However, the long-term roadmap for any reputable project involves a strategy of progressive decentralization. The state of a rollup's sequencer—whether it is centralized or has moved to a decentralized model—is one of the most important indicators of its maturity, security, and commitment to the core principles of web3.

## Understanding Rollup Stages: A Framework for L2 Maturity
Layer 2 scaling solutions, commonly known as rollups, are critical to Ethereum's future. To help users and developers navigate this complex ecosystem, a framework known as "Rollup Stages" was developed. Proposed by Ethereum co-founder Vitalik Buterin and implemented by the analysis platform L2BEAT, this system categorizes rollups based on their level of maturity and decentralization.

It is crucial to understand that these stages are not a direct measure of a rollup's security. Instead, they serve as a roadmap, tracking a project's progress from a centrally managed system with "training wheels" to a fully trustless, decentralized protocol governed entirely by on-chain smart contracts. You can track the stages of various projects on the L2BEAT scaling summary page.

## Stage 0: Full Training Wheels
Stage 0 represents the earliest and most centralized phase of a rollup's lifecycle. At this stage, the project relies heavily on a trusted team of operators and a Security Council—a committee of trusted individuals who can intervene to fix bugs, resolve issues, and push upgrades.

To qualify for Stage 0, a project must meet two key criteria:

Open-Source Data Reconstruction: The rollup's software must be open-source, allowing anyone to use the data published on the Layer 1 (L1) chain to reconstruct the rollup's state. This ensures transparency and the ability to independently verify the chain's integrity.

Limited User Exit: In the event of an unwanted upgrade or system failure, users have a short window (typically less than 7 days) to exit the system and withdraw their funds. This process often requires the cooperation of the centralized operator.

Stage 1: Enhanced Rollup Governance
A rollup graduates to Stage 1 when it makes significant strides towards decentralization, shifting key functions from a trusted team to on-chain smart contracts. The Security Council may still exist, but its role is often reduced to handling emergency bug fixes.

The defining features of a Stage 1 rollup include:

Operational Proof System: The rollup has a fully functional and decentralized fraud-proof or validity-proof system. This allows any participant to permissionlessly challenge or verify the correctness of state transitions on-chain.

Permissionless User Exit: The user exit process is improved. Users are given a longer window (more than 7 days) to withdraw their funds in response to an unwanted upgrade. Crucially, they can do so without needing permission or coordination from the rollup's operator.

Stage 2: No Training Wheels
Stage 2 is the final destination, representing a fully mature, decentralized, and trust-minimized rollup. At this stage, the "training wheels" have been removed, and the system operates almost entirely through autonomous, on-chain smart contracts.

A Stage 2 rollup is characterized by the following:

Full Smart Contract Governance: The rollup is completely managed by its on-chain smart contracts, removing reliance on any centralized operator.

Permissionless Proof System: The fraud or validity proof system is fully permissionless, allowing anyone to participate in validating the chain.

Limited Security Council: If a Security Council still exists, its powers are strictly limited to addressing errors that are adjudicated on-chain. This provides robust protection against potential governance attacks that could harm users.

Ample Exit Window: Users are provided with a substantial amount of time to exit the system before any proposed upgrade is executed, ensuring they can safely withdraw their assets if they disagree with a change.

## A Practical Analysis: zkSync Era's Stage 0 Risks
To see how this framework applies in practice, we can analyze zkSync Era, a prominent rollup currently at Stage 0, using the detailed risk analysis on L2BEAT. The site uses a color-coded pie chart to visualize five key risk vectors: green (low risk), yellow (medium risk), and red (high risk).

## Data Availability (Green):
The state of zkSync Era can be fully reconstructed from data published on L1. The rollup posts state differences ("state diffs") to Ethereum, which contains all the necessary information for anyone to construct proofs and verify the chain's state.

## State Validation (Green):
zkSync Era uses a ZK (Zero-Knowledge) proof system to ensure the integrity of its state transitions. Specifically, it employs the PLONK proof system with KZG commitments to cryptographically prove the validity of every transaction batch submitted to L1.

## Sequencer Failure (Yellow):
The Sequencer is the entity responsible for ordering transactions. If the zkSync Era Sequencer goes offline or censors users, the entire system halts. While users can submit transactions to an L1 queue, they cannot force the Sequencer to process them, effectively freezing all activity and withdrawals. This is a medium risk because while it freezes funds, it does not allow for theft and affects all users equally.

## Proposer Failure (Red):
The Proposer is responsible for submitting new state roots to the L1. On zkSync Era, this role is permissioned and managed by a whitelist. If these whitelisted Proposers fail or go offline, no new state roots can be published, and user withdrawals become frozen. This is considered a high risk because it directly prevents users from accessing their funds.

## Exit Window (Red):
zkSync Era's smart contracts are instantly upgradable by its governing entity. This means there is no delay or "exit window" for users to react to an unwanted or malicious upgrade. Since users have no time to withdraw their funds before a change takes effect, this presents a high risk.
