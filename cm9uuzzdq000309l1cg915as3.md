---
title: "Decentralizing Starknet: Architecture, Plans, and MEV Impact"
datePublished: Tue Sep 10 2024 23:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cm9uuzzdq000309l1cg915as3
slug: decentralizing-starknet-architecture-plans-and-mev-impact
canonical: https://dev.to/lordghostx/decentralizing-starknet-architecture-plans-and-mev-impact-7ck
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/ZiQkhI7417A/upload/6ccf2c968f7e21814f9b19ff58b56687.jpeg
tags: blockchain, web3, mev, starknet

---

## Introduction

Starknet is an Ethereum Layer 2 (L2) that leverages Zero-Knowledge (ZK) technology to deliver high transaction throughput and low costs while maintaining Ethereum's core principles. However, its reliance on a centralized sequencer for transaction processing introduces vulnerabilities such as single points of failure and scalability bottlenecks.

This article explores Starknet's current centralized architecture, the network's plan toward decentralization, the motivations driving this transition, and how these changes could reshape the future of the blockchain ecosystem, including the dynamics of [MEV (Maximal Extractable Value)](https://dev.to/lordghostx/introduction-to-mev-on-starknet-1o0l).

## Overview

Starknet's early success can be attributed to its centralized architecture, which facilitated rapid development. However, as the network scales, this centralization poses significant risks, including single points of failure and constraints on scalability.

Transitioning to a decentralized model is necessary to overcome these limitations and improve security through distributed control, enhance censorship resistance, and ensure long-term sustainability. This shift represents more than just a technical upgrade; it is pivotal for Starknet's continued growth and resilience.

[Starknet's move toward decentralization](https://www.starknet.io/blog/starknet-decentralization-a-roadmap-in-broad-strokes/) will set a new benchmark in the blockchain space, inspiring other networks to adopt similar models. This evolution can potentially strengthen the resilience and openness of the entire ecosystem.

Starknet's strategic plan, as detailed in its [introduction](https://community.starknet.io/t/starknet-decentralized-protocol-i-introduction/2671) and [presentations](https://www.youtube.com/watch?v=GAPU22K0YhY), outlines a gradual transition to decentralization, involving the distribution of control to independent nodes and the implementation of community-driven governance.

## Starknet Sequencer

The [Starknet Sequencer](https://docs.starknet.io/architecture-and-concepts/network-architecture/starknet-architecture-overview/#sequencers) is a specialized entity responsible for ordering and processing transactions before they are verified and submitted to Ethereum. Here's how it functions:

1. **Transaction Ordering**: The sequencer gathers and organizes incoming transactions in a specific order.
    
2. **Execution**: Once ordered, the sequencer executes these transactions.
    
3. **Batching**: After execution, the sequencer groups multiple transactions into a batch, known as a `rollup` for efficiency.
    
4. **Block Production**: The sequencer then produces blocks that contain these processed transaction batches.
    

![Starknet Transaction Flow](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/fzufikxx2v3hk5ka4x97.png align="left")

Beyond ordering transactions, the sequencer is also responsible for maintaining the network's liveness. It ensures that the network continues to process transactions and produce new blocks, keeping Starknet responsive and operational.

However, the centralization of sequencers introduces potential vulnerabilities, such as censorship, where a sequencer might choose to exclude certain transactions, and security risks, where a single point of failure could jeopardize the entire network.

## Decentralization Plans

The following initiatives highlight the steps Starknet is taking to distribute control, minimize vulnerabilities, and strengthen its infrastructure:

* **Community-run sequencers**: To mitigate the risks of centralized sequencers, Starknet plans to decentralize this role by enabling community-run sequencers. This will distribute power and responsibility across multiple entities, reducing the influence of any single sequencer and enhancing the system's overall resilience.
    
* **Decentralized transaction ordering**: By decentralizing the transaction ordering process, Starknet aims to prevent any single entity from controlling the sequence of transactions. This ensures a more transparent and equitable process, strengthens network security, and boosts participant trust.
    
* **Improved security and censorship resistance**: Deploying multiple sequencers will significantly enhance network security by reducing the risk of censorship and malicious activities. A decentralized sequencer network makes it far more difficult for any single actor to undermine Starknet's integrity, reinforcing its commitment to security and censorship resistance.
    

Due to its proven reliability and robustness, Tendermint has been proposed as a strong candidate for Starknet's [initial consensus mechanism](https://community.starknet.io/t/tendermint-for-starknet/98248) in its decentralization efforts.

## Tendermint

[Tendermint](https://tendermint.com/core/) is a Byzantine Fault Tolerant (BFT) consensus protocol used for securing decentralized networks. It allows a network of validators to agree on the next block in a blockchain, even if some validators are malicious, ensuring high reliability and security.

The protocol assumes there are **N = 3f + 1** validators, where **f** represents the number of potentially malicious (Byzantine) validators. Tendermint operates in two key dimensions: **height** (the current block) and **round** (the iterative process to reach consensus). Multiple rounds may be required for each block height, with a single leader or proposer selected per round. This structured approach ensures validators can securely and efficiently agree on the next block while maintaining decentralization and resilience.

### Consensus Process

* **Propose**: In the initial phase, a validator is chosen to propose a new block, typically through a round-robin selection, to ensure fair and systematic rotation among validators. This step starts the consensus process by submitting a candidate block for consideration.
    
* **Prevote**: After the block is proposed, validators cast their initial votes on whether to accept it. The `prevote` phase helps gauge initial support but does not finalize the block. It serves as an early signal of consensus among validators.
    
* **Precommit**: If a supermajority (usually two-thirds) of validators `prevote` in favor of the block, the process moves to the `precommit` stage. This stronger agreement shows a more definitive step towards finalizing the block, though it is not yet final.
    
* **Commit**: In the final phase, if a supermajority of `precommits` is reached, the block is committed to the blockchain. This step makes the block final and irreversible, securing it as a permanent part of the blockchain's history. The `commit` phase concludes the Tendermint consensus process, ensuring complete agreement among validators on the block.
    

![Tendermint Consensus Process](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/8yiyk7qlefyn8bb0ae83.png align="left")

## Starknet ProofChain

Starknet proposes using the Tendermint protocol with an innovative [ProofChain](https://community.starknet.io/t/starknet-decentralized-protocol-vii-chained-proof-protocols-braiding/18831) approach. This divides blocks into **K** proofchains based on height, where **ProofChain(H) = H mod K**. Each block contains proofs for the latest non-empty block and any empty blocks in its chain, improving security, scalability, and efficiency.

> Before diving into ProofChains, it's important to understand the two types of blocks:
> 
> * **Blocks containing transactions** (depicted in green) must include a proof to be valid.
>     
> * **Empty blocks** (depicted in red) contain no transactions and no proof.
>     

![ProofChain](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/lwwvpomlxxwzow1v0ree.png align="left")

Here are the key points:

* **Blocks divided into K ProofChains**: This structure allows parallel processing and verification, improving scalability by allowing multiple proofchains to be processed simultaneously.
    

![Dividing ProofChains](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/1rmf7l6kxlnlyhwh40yt.png align="left")

* **Each block contains proofs for previous blocks**: Every block includes cryptographic proofs for the preceding blocks, creating a robust verification chain. This ensures that the validity of each block is anchored in earlier blocks, securing the blockchain’s integrity.
    

![ProofChain Proofs](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/7xzajun77xm8ko3w1jm1.png align="left")

* **Enhanced security**: The braided ProofChain structure makes it significantly harder for an attacker to alter the blockchain's history. Recursive proofs, which include transaction execution and prior block verification, further reinforce network security.
    
* **Improved block verification efficiency**: By organizing proofs into different chains, the process of verifying new blocks is streamlined, reducing computational demands. This speeds up the consensus process, especially in larger networks.
    

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">The zero-knowledge proof (ZKP) required for the L1 state update can be efficiently constructed by folding <strong>K ZKPs</strong> (one from each ProofChain).</div>
</div>

![ProofChain Efficiency](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/u23kpuxp5tlcd7xgyldf.png align="left")

For example, **Block 12** must include a recursive proof that verifies the execution of transactions proof in **Block 3** and also validates the empty blocks **6** and **9**.

![ProofChain Demo](https://dev-to-uploads.s3.amazonaws.com/uploads/articles/ahfue42dl0luto5cfu9l.png align="left")

## Decentralization Challenges

By distributing the proposing, voting, and committing roles across many validators, Starknet reduces centralization risks while enhancing security and reliability. With its checks and balances, Tendermint's structured consensus process ensures that Starknet remains decentralized and trustless, which is essential for its long-term success and scalability.

However, there are several challenges to consider:

* **Network Latency**: Delays in the message (vote) delivery can cause timeouts and slow down the consensus process, impacting network performance.
    
* **Message Overhead**: Tendermint **O(n²)** message overhead increases with more validators, potentially reducing consensus efficiency as the network scales.
    
* **Speed Constraints**: The slowest validator limits the process, creating a bottleneck and vulnerability to delays as the validator count grows.
    

## Starknet Staking

Starknet is introducing STRK staking in Q4 2024 to boost decentralization and secure the network, becoming the first Layer 2 to enable such a mechanism. In this initial phase, neither StarkWare nor the Starknet Foundation will participate in staking, making them ineligible for staking rewards. Additionally, all locked tokens will not be eligible for staking during this phase.

%[https://youtu.be/_-SjLq3v4bw] 

See the [SNIP 18: Staking's First Stage on Starknet](https://community.starknet.io/t/snip-18-staking-s-first-stage-on-starknet/114334) proposal for more details.

## Network Rewards

Incentives will be essential for maintaining Starknet's security as it decentralizes. It will motivate participants to stake tokens and act in the network's best interest. This ensures long-term sustainability and attracts a diverse set of validators. The potential reward types include:

* **Transaction Fees:** Validators may earn a share of fees for processing transactions, aligning their incentives with network growth and usage.
    
* **Block Rewards:** Validators could receive newly minted tokens for proposing and validating blocks, providing a consistent base reward for their participation.
    
* **Proof Rewards:** Unique to Starknet, validators might be rewarded for generating and verifying cryptographic proofs, which is essential for maintaining the network's zero-knowledge proof system.
    
* **MEV (Maximal Extractable Value):** Validators can earn additional revenue by optimizing the transaction order within blocks. While profitable, it requires careful management to ensure fairness.
    
* **Staking Rewards:** Participants who stake tokens to secure the network may earn rewards, encouraging long-term commitment and a healthy distribution of staked tokens.
    

## Decentralization Impact on MEV

Decentralization tends to reduce the potential for extractive MEV (Maximal Extractable Value) by distributing power across a wider array of participants, reducing the chance of any single entity exploiting transaction ordering. However, it can also increase competition among validators, driving the emergence of more advanced and decentralized MEV strategies. Here are some predictions for the MEV landscape when Starknet becomes decentralized:

* **More equitable distribution of MEV opportunities**: As sequencing becomes decentralized, MEV capture will be more evenly distributed among participants, reducing concentration in the hands of a single entity and promoting fairer access.
    
* **Innovation in transparent MEV strategies**: Decentralization is likely to drive the development of new MEV strategies that are more transparent and equitable, enabling a level playing field for all participants.
    
* **Potential reduction in overall MEV:** Increased competition due to broader accessibility may decrease the total extractable MEV as more participants compete for the same opportunities, driving down the overall value that can be captured.
    
* **Emergence of specialized MEV extraction protocols**: The decentralized environment could lead to the rise of advanced tools and protocols designed for MEV extraction and a more distributed and competitive network.
    
* **Integration of MEV-resistant mechanisms**: Starknet and similar platforms may adopt protocol-level features to mitigate MEV exploitation, promoting greater fairness and stability within the network.
    
* **Shift towards community-oriented MEV distribution**: We may see a move towards models where MEV is distributed more equitably among network participants, potentially through community-driven MEV auctions or collective action mechanisms, reducing monopolization by a select few.
    

## Conclusion 👋

Starknet's transition to decentralization will significantly improve the network's security, scalability, and fairness. By decentralizing the sequencer role and implementing the Tendermint consensus protocol and ProofChain, Starknet aims to overcome centralization risks while preserving a trustless and resilient ecosystem.

If you're interested in diving deeper into MEV on Starknet, check out my previous posts:

* [Introduction to MEV on Starknet](https://dev.to/lordghostx/introduction-to-mev-on-starknet-1o0l)
    
* [Starknet Protocols: Key Features and MEV Opportunities](https://dev.to/lordghostx/starknet-protocols-key-features-and-mev-opportunities-574i)
    
* [Queries to Insights: Visualizing Starknet Data with Dune](https://dev.to/lordghostx/queries-to-insights-visualizing-starknet-data-with-dune-j8p)
    
* [Data to Queries: Exploring Starknet Data with Dune](https://dev.to/lordghostx/data-to-queries-exploring-starknet-data-with-dune-ihd)
    
* [Understanding Starknet Data with Dune Dashboards](https://dev.to/lordghostx/understanding-starknet-data-with-dune-dashboards-17je)
    

For additional resources on Starknet MEV, explore the comprehensive materials available here: [https://github.com/Icedcool/StarknetMEV/](https://github.com/Icedcool/StarknetMEV/).