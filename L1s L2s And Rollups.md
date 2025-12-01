Understanding Layer 1, Layer 2, and Rollup Scaling Solutions

https://updraft.cyfrin.io/courses/blockchain-basics/scalability/centralized-sequencers


The Ethereum network, like many foundational blockchains, faces a core challenge known as the Blockchain Trilemma. This concept posits that a blockchain can only optimize for two of three critical properties: decentralization, security, and scalability. Ethereum was designed to prioritize decentralization and security, which has made it the most robust and trusted smart contract platform. However, this focus comes at the cost of scalability, limiting the network to approximately 15 transactions per second. When demand is high, this limitation leads to network congestion and prohibitively expensive transaction fees, or "gas."

To solve this, developers created scaling solutions that can increase transaction throughput without sacrificing the security and decentralization of the main network. The most prominent of these are Layer 2 solutions, particularly Rollups. This lesson breaks down these foundational concepts.

What is a Layer 1 (L1) Blockchain?
A Layer 1, or L1, is the base protocol of a blockchain ecosystem. It is the fundamental network responsible for maintaining its own security and reaching a consensus on the state of the ledger. An L1 operates independently and does not rely on any other network for finality or security.

Because it serves as the ultimate source of truth where all transactions are eventually finalized, a Layer 1 is often referred to as the Settlement Layer. All transactions, including those processed on auxiliary layers, must ultimately settle on the L1 to be considered complete and irreversible.

Examples of Layer 1 Blockchains:

Ethereum Mainnet

Bitcoin

Solana

BNB Chain

Ethereum testnets, like Sepolia, also function as L1s in their respective environments.

What is a Layer 2 (L2) Blockchain?
A Layer 2, or L2, is a separate blockchain protocol built on top of a Layer 1. Its primary purpose is to extend the capabilities of the L1, most notably to improve scalability. An L2 processes transactions on its own chain but "hooks back into" the L1, inheriting the robust security and decentralization of its parent layer.

It is crucial to distinguish an L2 from a decentralized application (dApp). A dApp like Uniswap is a program that is deployed on an L1 blockchain. In contrast, an L2 is an entire, separate blockchain network designed to augment the L1 it is built upon.

Examples of Layer 2 Blockchains:

ZKsync

Optimism (OP)

Arbitrum

Polygon

Rollups: The Primary L2 Scaling Solution
Rollups are the most widely adopted type of L2 scaling solution. They work by executing transactions on the L2 chain (off-chain from the L1 perspective) and then bundling, or "rolling up," hundreds of them into a single, compressed transaction batch. This single batch is then posted to the Layer 1 network.

This process dramatically increases transaction throughput and reduces costs for end-users. The gas fee required to post the single batch to the L1 is shared across all the individual transactions contained within it, making each one significantly cheaper than if it were processed directly on the L1.

There are two primary types of rollups, distinguished by how they prove the validity of their transaction batches to the L1: Optimistic Rollups and Zero-Knowledge Rollups.

Optimistic Rollups
Optimistic Rollups operate on the principle that all transactions in a batch are valid by default—an "optimistic" assumption. After an L2 operator posts a batch to the L1, a Challenge Period begins, which typically lasts about a week.

During this window, any other network participant can scrutinize the batch. If they identify an invalid transaction, they can submit a Fraud Proof to the L1. This triggers a dispute resolution process on the L1 to verify the claim.

If the fraud proof is successful: The fraudulent batch is reverted, and the malicious operator who submitted it is penalized by having their staked collateral slashed.

If the challenge period ends without a successful challenge: The batch is considered final and is permanently recorded on the L1.

The primary trade-off of this model is the long waiting period for transaction finality. Users must wait for the challenge period to conclude before they can withdraw their funds from the L2 back to the L1.

Zero-Knowledge (ZK) Rollups
Zero-Knowledge (ZK) Rollups take a different approach. Instead of assuming validity and waiting for challenges, they proactively prove the validity of every transaction batch using advanced cryptography.

When a ZK-Rollup operator submits a batch to the L1, they also generate and submit a cryptographic Validity Proof, specifically a Zero-Knowledge Proof (ZKP). This proof mathematically guarantees that all the state changes within the batch are correct and follow the network's rules.

A smart contract on the L1, known as the Verifier, can instantly check this proof. If the proof is valid, the batch is accepted and finalized immediately. This eliminates the need for a lengthy challenge period, allowing for much faster withdrawals and finality compared to Optimistic Rollups.

A key feature of the ZKPs used in most rollups is succinctness, meaning the proof is very small and fast to verify, even if it represents an immense amount of computation. For this reason, these are sometimes called Succinct Rollups.

While most ZK-Rollups use this technology for scaling, some also leverage the "zero-knowledge" property to provide privacy, enabling secret balances and confidential transactions. These are often called ZK-ZK Rollups, with Aztec being a prominent example.

Conclusion
Layer 1 blockchains like Ethereum provide unmatched security and decentralization but struggle with scalability. Layer 2 solutions, and specifically Rollups, solve this problem by processing transactions on a separate, faster layer while inheriting the security of the L1. Optimistic Rollups achieve this through a fraud-proof system with a time delay, while ZK-Rollups use cryptographic validity proofs for instant finality. Together, these technologies enable the Ethereum ecosystem to scale, paving the way for mainstream adoption.

Centralized Sequencers: The Single Point of Failure in Blockchain Rollups
In the world of blockchain rollups, the sequencer is a critical component responsible for maintaining the flow and order of transactions. While essential for efficiency, the current implementation in most rollups presents a significant risk to the network and its users. This is because, in their current state, most sequencers are centralized.

A sequencer is a specialized operator whose primary job is to order transactions submitted by users. In some cases, it also performs the secondary role of bundling these ordered transactions together before they are submitted to the main blockchain. When this powerful role is controlled by a single entity, we call it a centralized sequencer. This concentration of power introduces two fundamental problems that strike at the core principles of blockchain technology: censorship and reliability.

The Risk of Censorship
A centralized sequencer controlled by a single party has the ultimate power to decide which transactions are included in a block and in what order. This opens the door for malicious behavior and censorship.

A dishonest sequencer could simply refuse to include transactions from specific users, effectively blocking them from using the network. Imagine trying to withdraw your funds from a rollup, only to have a malicious sequencer repeatedly ignore your transaction request. In this scenario, your assets are trapped, held hostage by a single entity.

Furthermore, a centralized sequencer can manipulate the order of transactions to extract financial value for itself, a practice known as Maximal Extractable Value (MEV). By reordering transactions, it can front-run trades or take advantage of arbitrage opportunities at the expense of ordinary users.

The Problem of Reliability: A Single Point of Failure
Beyond malicious intent, centralization creates a critical vulnerability: a single point of failure. The entire rollup network depends on this one sequencer to function. If that single entity goes offline for any reason—be it a technical glitch, a targeted attack, or a regulatory shutdown—the entire rollup halts.

When the sequencer is down, no new transactions can be processed. This means no deposits, no transfers, and, most importantly, no withdrawals. The network becomes completely unusable.

Consider the implications. What happens if you can't access your funds for a day? What if the outage lasts a week? A year? What if the sequencer never comes back online? If you cannot access or move your assets, their value is effectively zero. This existential threat undermines the very promise of user-owned assets that blockchain technology is supposed to guarantee.

The Solution: The Path to Decentralization
The clear and necessary solution to the risks of centralization is to decentralize the sequencer role. The end goal for any mature rollup is to have a system with multiple, independent sequencers who work together to order and process transactions.

In a decentralized model, if one sequencer goes offline or acts maliciously, others can step in to ensure the network remains live, operational, and fair. This redundancy eliminates the single point of failure and drastically reduces the risk of censorship, as no single party has ultimate control.

Projects in the space, such as zkSync, are actively working on this transition. Many rollups launch with a centralized sequencer for reasons of simplicity and rapid development in their early stages. However, the long-term roadmap for any reputable project involves a strategy of progressive decentralization. The state of a rollup's sequencer—whether it is centralized or has moved to a decentralized model—is one of the most important indicators of its maturity, security, and commitment to the core principles of web3.
