---
title: "Raising Block Limits on Solana: SIMD-0250 and Its Economic Effects"
datePublished: Thu Apr 24 2025 06:00:07 GMT+0000 (Coordinated Universal Time)
cuid: cm9uye8gz002709jy4rlk9uk5
slug: raising-block-limits-on-solana-simd-0250-and-its-economic-effects
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/yKzECK-O9-k/upload/8117101838cea4c0eafee73b82e0dc7a.jpeg
tags: research, blockchain, web3, solana

---

[SIMD-0250](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0250-raise-block-limits-to-60M.md), a Solana Improvement Document (SIMD), proposes raising the network’s maximum block Compute Units (CUs) to 60 million. It builds on [SIMD-0207](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0207-raise-block-limits-to-50M.md), which proposes to raise the limit from 48 million to 50 million. The only change made in both proposals is to the **Max Block Units**. Other limits, like **Max Writable Account Units**, **Max Vote Units**, and **Max Block Accounts Data Size Delta**, stay the same. This means more room for non-vote transactions, which helps boost total transaction throughput.

With more space in each block, the network can process more transactions per second. This helps reduce congestion and lowers the need for users to outbid each other on fees, especially during periods of high demand. For apps handling a high volume of transactions or those with small dollar values (like games or microtransactions), this kind of fee relief is a big deal. [Messari’s State of Solana Q3 2023](https://messari.io/report/state-of-solana-q3-2023) report pointed to low fees and fast confirmations as major drivers of growth in consumer-facing use cases like gaming, DePIN, and social platforms.

For validators, this change comes with trade-offs. While it can increase fee earnings, it also raises hardware demands. Over time, this could raise the bar for running a validator and limit participation to better-resourced operators. This concern is not new. The [Solana Foundation’s March 2023 Validator Health Report](https://solana.com/news/validator-health-report-march-2023) already emphasized that validator performance and resilience will need to scale alongside network growth.

Developers, on the other hand, stand to gain from the extra compute available. Apps that rely on batching transactions or updating on-chain state in real-time, like DeFi order books, multiplayer games, or NFT platforms, can run more smoothly. The larger block size also gives the network better breathing room during traffic spikes, helping avoid lags or dropped transactions when demand rises.

Still, there are risks. Larger blocks may take longer to execute and propagate across the network, placing extra load on RPC nodes, explorers, and indexers. If these systems are not updated to handle the new block size, they might lag or fail to keep up. The change is backward-compatible, but outdated nodes could reject the new, larger blocks entirely.

In summary, [SIMD-0250](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0250-raise-block-limits-to-60M.md) makes a relatively simple change with wide-ranging effects. It expands Solana’s ability to handle more activity, improve app performance, and stabilize fees, but it adds more pressure on infrastructure and validators. Like many scalability upgrades, its success depends on how well the ecosystem keeps up and whether that added capacity translates into meaningful new use.

## References

* SIMD-0207: Raise the Block Limit to 50M CUs. Retrieved from [https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0207-raise-block-limits-to-50M.md](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0207-raise-block-limits-to-50M.md)
* SIMD-0250: Increase Block Limit to 60M CUs. Retrieved from [https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0250-raise-block-limits-to-60M.md](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0250-raise-block-limits-to-60M.md)    
* Messari’s State of Solana Q3 2023. Retrieved from [https://messari.io/report/state-of-solana-q3-2023](https://messari.io/report/state-of-solana-q3-2023)
* Solana Foundation Validator Health Report: March 2023. Retrieved from [https://solana.com/news/validator-health-report-march-2023](https://solana.com/news/validator-health-report-march-2023)