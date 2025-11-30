# 📜 Ethereum's Rollup-Centric Roadmap and Key Upgrades

This timeline details the past, current, and future milestones that define Ethereum's strategy to scale primarily through Layer-2 rollups.

## ⏳ Past Milestones (Foundation)

| Date | Upgrade/Event | Key Impact on Scaling |
|---|---|---|
| Oct 2020 | Vitalik Buterin publishes "A rollup-centric Ethereum roadmap" | Officially signaled the pivot in scaling strategy from sharding-first to rollup-first. |
| Dec 2020 | Beacon Chain Launch | Introduced the Proof-of-Stake (PoS) mechanism on a parallel chain. |
| Sep 2022 | The Merge | Ethereum transitions its consensus mechanism from Proof-of-Work to Proof-of-Stake (PoS). Energy usage dropped by 99.95% Set the fundamental security layer for rollups. |
| Apr 2023 | Shapella (Shanghai + Capella) | Enabled staked ETH withdrawals, significantly improving validator flexibility and capital efficiency. |
| Mar 2024 | Dencun Upgrade | Introduces EIP-4844 (Proto-Danksharding). This added new "blob" transactions for cheaper Layer-2 data posting, dramatically reducing L2 fees and congestion. |

## 🎯 Current Phase (2025: Scaling & User Experience)

| Date | Upgrade Name | Key Features & Rollup Impact |
|---|---|---|
| May 2025 | Pectra Upgrade | Account Abstraction (EIP-7702): Brings "smart wallet" features to all users, simplifying the user experience for interacting with rollups. Increased Blob Throughput: Raises the target for blobs (from 3 to 6, max 9), improving Layer-2 data capacity. |
| Dec 2025 | Fusaka Upgrade | PeerDAS (Peer Data Availability Sampling): Enables efficient and decentralized verification of rollup data. Blob-Only Forks: Allows for rapid, modular adjustments to scaling parameters. Scaling Potential: Prepares Ethereum's base layer to support rollups reaching >100,000 TPS combined. |

## 🔮 Future Milestones (Mass Adoption & Decentralization)

| Date | Phase/Upgrade | Key Objectives |
|---|---|---|
| 2026 | Glamsterdam Upgrade | Continues the path of the Surge/Verge/Purge roadmap. Focuses on more blob scaling (moving toward full Danksharding) and further decentralization of rollups (e.g., removing centralized sequencers). |
| 2027–2030 | Modular & Decentralized Ethereum | Full Danksharding: Complete data availability sampling and modular scaling. Rollup Decentralization: Implementation of permissionless sequencers, trustless bridges, and decentralized proof generation. |

### Long-Term Goal

The long-term vision is a Modular Ethereum Stack, where:

 * Ethereum L1 serves as the security, data availability, and settlement layer.
 * Rollups (L2s) handle execution and vastly increase transaction throughput.
 * Target: >100,000 Transactions Per Second (TPS) across the L2 ecosystem.

## 🗺️ Roadmap Phases (Vitalik's Framework)

Vitalik Buterin's conceptual framework organizes the network's evolution into distinct, multi-year phases:
 * Merge: PoS transition (✅ Completed)
 * Surge: Rollup scaling, data sharding, and Proto-Danksharding (currently Ongoing)
 * Verge: Achieving Statelessness via Verkle Trees
 * Purge: History pruning and protocol simplification
 * Splurge: Miscellaneous User Experience (UX) and performance improvements

### Would you like to explore the technical mechanisms of PeerDAS from the Fusaka upgrade or the Account Abstraction features in Pectra?

Both PeerDAS (Fusaka) and Account Abstraction (Pectra) are revolutionary for different reasons: PeerDAS addresses the core technical problem of data scalability, while Account Abstraction addresses the core user problem of experience.

Here is a detailed, two-part breakdown of these key rollup-centric upgrades:

## 1. ⚙️ Fusaka Upgrade: PeerDAS (EIP-7594)

The Fusaka upgrade, scheduled for December 2025, centers on implementing Peer Data Availability Sampling (PeerDAS). This is a crucial step towards Full Danksharding and is designed to dramatically increase the data space available to rollups, which directly translates into lower and more stable Layer-2 fees.

### Technical Mechanism: Data Availability Sampling (DAS)

| Feature | Pre-PeerDAS (Current EIP-4844) | Post-PeerDAS (Fusaka) |
|---|---|---|
| Data Verification | Every full Ethereum node must download and store all the "blob" data posted by rollups to verify data availability. | Nodes download and store only a small, random fraction (e.g., 1/8th) of the blob data. |
| Data Security | Relies on every node checking all data. | Relies on cryptographic techniques like Erasure Coding and KZG Proofs. If a malicious entity withholds data, the random sampling will detect it with a very high probability as low as 10<sup>(-24) error probability. |
| Scaling Impact | Limited data capacity, which leads to fee spikes when Layer-2 demand exceeds the current blob limit. | Allows for an 8x (or more) increase in total blob capacity without requiring nodes to significantly upgrade hardware or bandwidth. |
| Fee Reduction | Fees are reduced compared to calldata, but costs spike with high demand. | The massive increase in capacity should lead to lower, more predictable, and sustained low fees on Layer-2 networks. |

Why it's a Game Changer for Rollups

Rollups rely on posting compressed transaction data to the Ethereum mainnet in blobs. The cost of posting this data is the single largest component of an L2 user's transaction fee. By enabling the network to safely handle 8x more data, PeerDAS alleviates this supply bottleneck, allowing rollups to scale their transaction throughput to potentially >100,000 TPS combined.

## 2. ✨ Pectra Upgrade: Account Abstraction (EIP-7702)

The Pectra upgrade (scheduled for early 2025) focuses on EIP-7702, a new form of Account Abstraction (AA) that addresses the shortcomings of the traditional Ethereum user experience (UX) to make using rollups and L1 seamless and intuitive.

### Features of EIP-7702

EIP-7702 introduces a new transaction type that allows an Externally Owned Account (EOA)—your standard private-key wallet like MetaMask—to temporarily adopt the functionality of a Smart Contract Account (SCA) during the execution of a transaction.

| Feature | Traditional EOA (Before Pectra) | EIP-7702 Enabled EOA (Pectra) |
|---|---|---|
| Transaction Flow | Requires multiple steps (e.g., Approve then Swap). Gas must be paid in ETH. | Batch Transactions: Multiple steps (like Approve and Swap) can be combined into a single transaction and signed once. |
| Gas Payment | Only the native asset (ETH) can pay for gas. | Sponsored Gas: Allows a smart contract (a "paymaster") to pay gas fees for the user, enabling gasless transactions or payment in stablecoins/ERC-20 tokens. |
| Security & Recovery | Single private key is the only point of control and failure. | Enables complex logic like social recovery, session keys (pre-approving certain actions for a limited time), and spending limits. |
| Wallet Migration | Required users to deploy a separate smart contract wallet and transfer all assets (costly and complex). | Allows existing EOAs to gain smart-wallet features without creating a new address or migrating assets. |

Why it's a Game Changer for Rollups?

Rollups are already cheap, but they are often difficult for new users to navigate. Account Abstraction solves this by:

 * Simplifying DeFi UX: Turning multi-click, multi-signature actions into one-click confirmations.
   
 * Onboarding New Users: Eliminating the need for users to hold native ETH for gas, which is a major barrier to entry.

Together, PeerDAS (lowering the cost of Layer-2s) and Account Abstraction (lowering the complexity of Layer-2s) represent the dual focus of Ethereum's roadmap to achieve mass adoption.

### Would you like to explore the concept of Full Danksharding and how it builds on PeerDAS?

That's the ultimate destination on the Ethereum roadmap. Full Danksharding is the culmination of all the previous scaling efforts and represents the point where Layer-2 transaction fees are driven near zero, enabling truly mass-market applications.

Here is a detailed breakdown of Full Danksharding, how it builds on PeerDAS, and the other concurrent infrastructure upgrades.

# 🏗️ Full Danksharding: The Final Scaling Frontier

Full Danksharding (projected target 2026–2027) is the final stage of the "Surge" phase. It focuses purely on maximizing the data availability layer for rollups.

## 1. How it Builds on PeerDAS
   
| Feature | PeerDAS (Fusaka Upgrade) | Full Danksharding |
|---|---|---|
| Data Verification | Implements the core technical mechanism: Data Availability Sampling (DAS). Nodes sample data to verify its availability without downloading the full data set. | Retains DAS but may upgrade to 2D Erasure Coding and more advanced sampling techniques for maximum safety and efficiency. |
| Blob Capacity | Increases the target number of blobs per block from \approx 3 (Dencun) to a higher, modular target (e.g., up to 6 to 48). | VASTLY increases the target to a total of 64 shards/blobs per block. |
| Goal | Securely unlock an initial 8 \times capacity increase without increasing node hardware requirements. | Achieve the maximum possible safe data capacity on the Layer 1 network to support the most extreme Layer-2 demand. |
| Result | Significantly lower and more predictable L2 fees. | Near-zero, stable L2 fees, unlocking web-scale applications like decentralized social media and high-frequency micropayments. |

The relationship is simple: PeerDAS (EIP-7594) implements the crucial, complex technology (DAS) required for safe data scaling. Full Danksharding is the final act of turning the dial up to the maximum capacity once that technology is fully proven and adopted.

## 2. 🔑 Key Concurrent Upgrades

Full Danksharding is not the only thing happening. Several other upgrades are essential for creating the robust, scalable network Ethereum envisions:

### A. Verkle Trees (The Verge Phase)

| Concept | Impact on Scaling and Rollups |
|---|---|
| State Compression | Verkle trees replace the existing Merkle Patricia Tree data structure. They use a cryptographic method that allows validators to verify the entire state of Ethereum with a dramatically smaller proof (witness). |
| Statelessness | This significantly reduces the storage and bandwidth burden on nodes. It makes it easier and cheaper for anyone to run a full Ethereum node (e.g., on a basic machine), strengthening decentralization—which is the ultimate security layer that rollups rely on. |
| L1 Throughput | The ability to efficiently verify blocks with smaller proofs may safely allow for an increase in the Layer-1 execution gas limit, providing another layer of throughput increase. |


### B. Rollup Decentralization (The Purge Phase)

| Concept | Impact on Scaling and Rollups |
|---|---|
| Decentralized Sequencers | Today, most rollups rely on a single, centralized entity (the Sequencer) to order transactions and send them to Ethereum. This creates a risk of censorship and downtime. |
| Based Rollups / Shared Sequencers | The roadmap includes moving to decentralized sequencing (e.g., using Ethereum's own validator set to sequence L2 transactions, known as "Based Rollups," or using a shared decentralized network). This eliminates the single point of failure and makes the entire rollup ecosystem as trustless as Ethereum L1. |


### C. Single Slot Finality (SSF)

| Concept | Impact on Scaling and Rollups |
|---|---|
| Faster Finality | Aims to reduce the time it takes for a block to be finalized from the current \approx 13 minutes to a single 12-second slot. |
| Improved L2 Bridges | SSF is critical for fast, secure cross-rollup communication. It will dramatically reduce the waiting period required for assets to move between Layer-2 networks, making the entire ecosystem feel more unified. |

In summary, the journey to Full Danksharding is about more than just data capacity; it's about marrying maximum throughput (Danksharding) with better user experience (Account Abstraction) and enhanced decentralization (Verkle Trees & Sequencer Decentralization).

### Do you have any questions about the security implications of Data Availability Sampling, or would you like to know more about the Verkle Trees upgrade?

Understanding the security model of Data Availability Sampling (DAS) and the technical benefits of Verkle Trees explains how Ethereum scales without compromising its core value: decentralization.

Here is a detailed breakdown of both concepts:

## 1. 🛡️ Security Implications of Data Availability Sampling (DAS)

Data Availability Sampling (DAS) is the core mechanism behind PeerDAS (Fusaka upgrade) and Full Danksharding. It solves the Data Availability Problem—ensuring that the rollup transaction data is available for users to download and verify—without requiring every node to download the entire massive dataset.

### The Problem DAS Solves

As Ethereum scales to handle 64 blobs per block (Full Danksharding), the total data in a block could easily exceed the bandwidth limits of a typical home user running a full node. If a block producer (validator) publishes a block header but withholds the actual blob data, a malicious state transition could be hidden from the network, making it impossible for users to challenge fraud proofs or withdraw their funds.

### How DAS Maintains Security

DAS uses a combination of two advanced cryptographic techniques:

A. Erasure Coding

 * The original data (N pieces) is expanded into a much larger, redundant dataset (M pieces, usually M = 2N).
 * Crucially, the original data can be fully reconstructed from any N of the M pieces. This introduces redundancy. If a block producer withholds up to half of the data, the network can still recover the original set.
   
B. Random Sampling

 * Instead of downloading the entire block, light clients (and nodes) randomly request and check only a few samples (e.g., 50 samples) from the M pieces of the expanded data.
 * If the samples requested are successfully retrieved and verify correctly (using KZG proofs), the client has extremely high statistical confidence (a near 100% certainty) that all the data is available.
   
By checking 50 samples, the probability of missing a block where 50% of the data is withheld is virtually zero.

### Security and Decentralization Benefits

| Benefit | Description |
|---|---|
| Preserves Decentralization | By eliminating the need for every node to download huge amounts of data, DAS keeps hardware requirements low. This ensures that solo stakers and hobbyists can continue running nodes, which is the ultimate safeguard against censorship and single-party control. |
| Security for Light Clients | DAS empowers light clients (users running a simple wallet on their phone) to verify data availability securely and trustlessly, without relying on full nodes or centralized data committees (DACs). |
| Fraud Proof Efficacy | It guarantees that if a rollup posts a fraudulent state root to L1, the necessary transaction data will be available for an honest party to reconstruct the state and submit a fraud proof. |

## 2. 🌳 Verkle Trees (The Verge Phase)

Verkle Trees are an upcoming upgrade (part of the Verge phase) that replaces the current cryptographic data structure used to store Ethereum's state: the Merkle Patricia Trie. This upgrade focuses on making the network more efficient for nodes.

### The Problem Verkle Trees Solve

When a full Ethereum node executes a transaction, it needs to generate a witness (or proof) to verify that the state change (like updating an account balance) is valid.

 * In the current Merkle Patricia Tries, this witness can be large (up to several kilobytes), requiring nodes to transmit a lot of data during block verification.
 * The large witness size is a major obstacle to achieving true Statelessness—the ability for a node to verify a new block without having to store the entire state of the blockchain.
   
### How Verkle Trees Work
Verkle trees use advanced cryptography, specifically Vector Commitments and Polynomial Commitments (like KZG), to create a highly efficient data structure.

| Feature | Merkle Patricia Tries (Current) | Verkle Trees (Future) |
|---|---|---|
| Proof/Witness Size | Large (e.g., approx 1 kilobyte for a billion data pieces). | Extremely Small (e.g., < 150 bytes for the same data set). This is a 10-20X times} reduction. |
| Proof Mechanism | Requires providing all sister nodes on the path from the data leaf up to the root. | Requires providing only the path and a single, fixed-size proof that verifies all parent-child links. |
| Efficiency | Slower verification as the state grows deeper. | Constant-sized, fast verification regardless of the state size. |

The Impact on Ethereum's Decentralization
The primary benefit of Verkle trees is enabling Statelessness and reducing the burden on nodes:
 * Lower Barrier to Entry: Since a node only needs to download the tiny witness to verify a block, the cost of running a full node is dramatically reduced. This is a massive boost to decentralization.
 * Faster Sync Times: New nodes can sync and start verifying the chain much faster, as they don't need to download the full state database, only the small proofs.
 * Safer L1 Gas Limit Increase: By making block verification faster and lighter, the network gains a safer pathway to potentially increasing the Layer 1 gas limit, which provides more room for rollups to grow their data throughput.


Both DAS and Verkle Trees are essential components of Ethereum's "Surge" and "Verge" phases, tackling scaling and decentralization, respectively.

