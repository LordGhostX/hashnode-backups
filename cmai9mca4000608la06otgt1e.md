---
title: "SIMD-0204: Building the Foundation for Slashing on Solana"
datePublished: Sat May 10 2025 13:33:03 GMT+0000 (Coordinated Universal Time)
cuid: cmai9mca4000608la06otgt1e
slug: simd-0204-building-the-foundation-for-slashing-on-solana
cover: https://cdn.hashnode.com/res/hashnode/image/stock/unsplash/tn0j4ZwTpgU/upload/3647148bd46e5f268368217b8c2d5886.jpeg
tags: blockchain, cryptocurrency, web3, solana

---

## Introduction

In proof-of-stake blockchains, validators are responsible for keeping the network secure and functioning by verifying transactions and voting on consensus. To make sure they act honestly, many networks implement slashing. Slashing is a mechanism that punishes validators with bad intentions by taking away some of their staked tokens \[1\].

Blockchains like Ethereum, Cosmos, and Polkadot already rely on slashing to discourage bad behavior, like double signing or going offline for too long. The idea is to make attacks expensive and encourage delegators to support trustworthy validators \[2\].

Solana, known for its high speed and performance, hasn’t had slashing built into the protocol \[3\]. Validators could act against the network’s best interests without facing consequences. That’s starting to change with [SIMD-0204: Slashable Event Verification](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0204-slashable-event-verification.md) \[4\], which introduces an on-chain program for verifying and recording validator violations. It sets up the infrastructure for slashing to become part of Solana’s future.

## What Is Slashing?

Slashing is a protocol-level mechanism used in many proof-of-stake networks to penalize validators that break the rules. When a validator misbehaves, a portion of their staked tokens can be removed or “slashed” to discourage malicious behavior and reinforce honest participation \[1\]\[2\].

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1746879434581/2ef18a8a-bbd2-4395-8704-a4ec095e0f90.png align="left")

**Figure: Slashing Activity on Ethereum**

This dashboard from [Rated Explorer](https://explorer.rated.network/slashings?network=mainnet) \[5\] shows real-time data on validator slashing in Ethereum, including the total number of penalized validators, how often slashing has occurred, and penalty sizes. As of May 10, 2025, 474 validators have been slashed across 129 separate incidents.

The types of behavior that lead to slashing vary by network, but common examples include:

* **Double Signing**: Signing two different blocks at the same [block height](https://academy.binance.com/en/glossary/block-height) or [block slot](https://docs.chainstack.com/docs/understanding-the-difference-between-blocks-and-slots-on-solana).
    
* **Equivocation**: Sending conflicting messages or blocks to different parts of the network during consensus.
    
* **Downtime**: Failing to stay online and participate in consensus harms the network’s reliability and can lead to slashing.
    
* **Surround Voting**: Submitting votes that contradict earlier ones to try to split the network or manipulate the network.
    

Some proof-of-stake networks also offer rewards to people who detect and report violations. If you submit valid proof of misbehavior, you might get a share of the slashed tokens \[13\]. This helps keep everyone watching and incentivizes the community to hold validators accountable.

## Why Slashing Is Challenging on Solana

Slashing has been part of many proof-of-stake networks for years, but Solana didn’t build it into the protocol early on. That’s mainly because of how the network is designed.

Solana is built for speed and performance. Validators are expected to cast and share votes quickly, sometimes before memory is fully written to disk. This helps the network stay fast, but it also increases the risk of local-state corruption \[3\]. In this kind of environment, automatic slashing becomes risky since it could end up punishing validators for errors that aren’t malicious.

Another challenge is that many types of misbehavior aren’t easy to detect synchronously. Things like duplicate blocks or invalid votes often need to be verified after they happen by collecting and comparing data from across the network \[4\]. That makes instant punishment harder to get right.

To handle this, Solana has mostly relied on softer tools like fork choice rules and gossip between validators \[6\]. These can help correct the network when things go wrong, but they don’t come with penalties or keep a record of what happened. For a network that values speed and resilience, slashing has always been a tricky thing to get right.

## SIMD-0204: Solana’s First Step Toward Slashing

Slashing on Solana needed a starting point. The network needed a way to prove, not just suspect, that a validator misbehaved. **SIMD-0204** provides that foundation by introducing a new on-chain program that verifies and records certain types of validator violations.

The program focuses on a specific violation to start: `duplicate block production`. If a valid proof is submitted, the violation is recorded on-chain. These reports don’t result in slashing yet, but they create a permanent, verifiable record of the violation.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Duplicate block production has caused real-world issues. In September 2022, a validator malfunction led to duplicate blocks in the same slot, triggering a consensus failure and halting Solana's mainnet for over 8 hours [14].</div>
</div>

This step is intentionally limited in scope. **SIMD-0204** doesn’t modify stakes or rewards. Its goal is to establish observability, giving the protocol, developers, and stakers a shared view into validator behavior that’s backed by cryptographic proof, not rumor or assumption.

### How SIMD-0204 Works

When **SIMD-0204** is activated, it deploys the [Slashing Program](https://github.com/solana-program/slashing) \[7\]. This program is enshrined, meaning it's deployed directly by the protocol at a fixed address. To report a violation, a user submits proof using the `DuplicateBlockProof` instruction. This proof contains two conflicting shreds for the same slot, signed by the same validator. The program performs a series of checks:

* It verifies that the shreds are formatted correctly.
    
* It confirms that the shreds represent a real conflict using known duplication criteria.
    
* It uses the [Ed25519 Program](https://solana.com/docs/core/programs) \[8\] to ensure the same validator signed both shreds.
    

There’s also a second instruction, `CloseViolationReport`, which lets the reporter reclaim the lamports they paid to store the proof. That’s only allowed after a waiting period of three (3) epochs to give indexers and dashboards time to process the data.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">Thanks to <a target="_self" rel="noopener noreferrer nofollow" href="https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0180-vote-account-leader-schedule.md" style="pointer-events: none">SIMD-0180: Vote Account Address Keyed Leader Schedule</a> [9], block production is now linked to vote accounts rather than validator identities. This makes it possible to associate reported violations with the exact stake that earned a leader slot, which helps slashing in the future.</div>
</div>

Future proposals like [SIMD-0212: Slashing](https://github.com/solana-foundation/solana-improvement-documents/pull/212) \[10\] will build on this reporting system to enforce actual penalties. But for now, the focus is on proving the network can detect and record violations reliably without introducing risk for honest validators.

### What’s Stored On-Chain

After a proof passes verification, the program stores the violation in a new `ProofReport` account at a [Program Derived Address (PDA)](https://www.alchemy.com/overviews/program-derived-address). This account contains metadata about the violation:

```rust
struct ProofReport {
    version: u8,            // Current version of the report format
    reporter: Pubkey,       // Public key of the fee payer who submitted the report
    destination: Pubkey,    // Account to receive lamports when the report is closed
    epoch: Epoch,           // Epoch when the report was created
    violator: Pubkey,       // Public key of the violating validator
    slot: Slot,             // Slot at which the violation occurred
    violation_type: u8,     // Type of violation (e.g., duplicate block)
}
```

In addition to the metadata, the account also stores the submitted proof for future reference:

```rust
struct DuplicateBlockProofData {
    shred1_length: u32,    // Length in bytes of the first shred
    shred1: &[u8],         // Byte array containing the first shred’s data
    shred2_length: u32,    // Length in bytes of the second shred
    shred2: &[u8],         // Byte array containing the second shred’s data
}
```

This combination ensures that each report includes not only who did what and when but also the raw data needed to verify the violation later.

### Security and Design Considerations

The Slashing Program is deployed using the `BPF Upgradeable Loader`, but its upgrade authority is set to `None`. This means the program cannot be updated after it’s deployed, ensuring consistent behavior and preventing tampering, even by core developers.

<div data-node-type="callout">
<div data-node-type="callout-emoji">💡</div>
<div data-node-type="callout-text">During the <a target="_self" rel="noopener noreferrer nofollow" href="https://github.com/solana-foundation/solana-improvement-documents/pull/204#discussion_r1866733325" style="pointer-events: none">design review</a> [11], there was discussion about whether to use the newer <code>Loader-v4</code>. The team chose to stick with the <code>BPF Upgradeable Loader</code> for compatibility while leaving the door open to switch if <code>Loader-v4</code> becomes standard in the future.</div>
</div>

To prevent abuse or noise in the program:

* The program checks whether a `ProofReport` for the same slot and violator already exists. If it does, the new submission is rejected.
    
* Reports must be properly formed and cryptographically verifiable because malformed or replayed submissions are automatically discarded.
    
* A future slashing mechanism will rely on this data, so accuracy and trust in the program matter. **SIMD-0204** starts with just one type of violation: `duplicate block production`. This focused approach keeps things manageable while developers and the community build confidence in the program.
    

### Technical Details to Highlight:

* **Epoch-bound Activation**: The program is activated at the boundary of a new epoch when the feature flag is turned on. This keeps integration predictable and reduces risk during rollout.
    
* **Submission Deadline**: To prevent stale or delayed reports, proofs must be submitted within approximately one epoch’s worth of slots (432,000 \[6\]) from when the violation occurred.
    
* **No Rewards or Penalties (Yet)**: The program does not currently enforce any penalties or modify validator rewards. Its role is limited to verifying and recording. Future proposals, such as **SIMD-0212**, are expected to introduce the mechanisms for enforcing penalties based on these reports.
    
* **Byte-Level Validation**: The program performs low-level operations, including fixed-size buffers, little-endian encoding, and offset-based deserialization to interpret and validate proofs. Signature verification is handled through instruction introspection and signature offsets.
    
* **Minimum Report Retention**: Reports must remain on-chain for at least three (3) epochs before they can be closed. This window gives dashboards and indexers time to process the data. Only one (1) epoch is truly needed, so this may be reduced in the future.
    

## Building Validator Transparency

**SIMD-0204** lays the groundwork not only for future slashing but also for observing validator behavior in a consistent and verifiable way. Anyone can submit valid proof of violation: validators, bots, monitoring tools, or independent observers running their infrastructure \[12\]. This open access helps ensure the network is not solely reliant on core validators to flag bad actors.

As more reports are recorded on-chain, third-party tools are expected to build on top of this data. Dashboards, indexers, and analytics platforms can track validator behavior over time, creating the foundation for metrics like a slashing index or reputation score. These tools help delegators make better-informed staking decisions and encourage validators to maintain good practices \[12\].

While no rewards or penalties are enforced yet, this proposal provides a starting point for community-driven transparency. Future incentive structures will likely rely on it.

## What’s Next for Slashing on Solana?

**SIMD-0204** introduces the core infrastructure for recording validator violations, but actual slashing requires policy, economics, and enforcement. That’s where future proposals like **SIMD-0212** come in. This upcoming proposal focuses on how penalties are calculated and applied using the reports generated by **SIMD-0204**.

Other improvements and alternatives being discussed include \[10\]\[12\]:

* **New Violation Types**
    
    * Double voting
        
    * Unstaking before a report is recorded (to avoid penalties)
        
* **Potential Protocol Changes**
    
    * A cooldown extension: allowing reports to still be submitted after stake deactivation.
        
    * Shorter epochs: reducing the time window during which bad actors can operate before being penalized.
        
* **Design Alternatives**
    
    * Slashing only the validator’s self-stake.
        
    * Deactivating stake without slashing.
        
    * Rewarding reporters for submitting valid proofs.
        
    * Redirecting slashed funds to an insurance pool or protocol treasury.
        

These decisions will shape how aggressive or conservative Solana’s slashing policy becomes. Community input and governance will play a key role as slashing evolves.

## Implications for the Solana Ecosystem

As with any major protocol change, the introduction of slashing introduces both opportunity and complexity across the Solana ecosystem \[4\]\[10\]\[12\].

### Potential Benefits

* Increased validator accountability and overall network safety.
    
* Enables new incentive structures and more robust slashing economics.
    
* Transparent violation history (via `ProofReports`) supports third-party tools that help delegators evaluate validator behavior.
    
* Slashing risk may encourage delegators to spread stakes across more validators, supporting decentralization.
    

### Challenges and Trade-Offs

* **False Positives**: If verification logic fails, honest validators could be penalized, so rigorous testing and auditing are needed.
    
* **Deterrence Risk**: Without clear education and framing, slashing could discourage new or smaller participants from staking.
    
* **Operational Overhead**: Adds complexity for indexers, RPC providers, and dashboard maintainers.
    
* **Governance Sensitivity**: Activating slashing and tuning its parameters require broad community alignment.
    
* **Incentive Complexity**: Reward systems (e.g., for reporters) must be carefully designed to avoid issues like frontrunning, manipulation, or abuse.
    

## Conclusion

[SIMD-0204: Slashable Event Verification](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0204-slashable-event-verification.md) \[4\] marks a key milestone in Solana’s efforts to promote validator accountability. It introduces an on-chain program for verifiable reporting of validator violations, building the foundation for future slashing logic, economic policy, and governance participation.

By making validator behavior observable and provable, **SIMD-0204** empowers dashboards, analytics platforms, and delegators to make more informed decisions. As proposals like [SIMD-0212: Slashing](https://github.com/solana-foundation/solana-improvement-documents/pull/212) \[10\] are implemented, this positions the network for a more secure and decentralized validator landscape.

Looking ahead, the success of slashing in Solana will depend on real-world use. Signals such as valid reports submitted, adoption by validators and tooling, and implementation of slashing mechanisms will matter. A decline in duplicate blocks and growth in tools built around `ProofReports` would point to increased transparency and accountability across the network.

## References

\[1\] Ledger Academy. “​​What is Slashing in Crypto?”. [https://www.ledger.com/academy/topics/blockchain/what-is-slashing](https://www.ledger.com/academy/topics/blockchain/what-is-slashing)  
\[2\] Binance Academy. “Slashing”. [https://academy.binance.com/en/glossary/slashing](https://academy.binance.com/en/glossary/slashing)  
\[3\] Agave Validator Documentation. “Slashing Roadmap”. [https://docs.anza.xyz/proposals/optimistic-confirmation-and-slashing](https://docs.anza.xyz/proposals/optimistic-confirmation-and-slashing)  
\[4\] Ashwin Sekar. “SIMD-0204: Slashable Event Verification”. [https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0204-slashable-event-verification.md](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0204-slashable-event-verification.md)  
\[5\] Rated Explorer. “Slashing Activity Dashboard”. [https://explorer.rated.network/slashings](https://explorer.rated.network/slashings)  
\[6\] Ryan Chern, Helius. “Consensus on Solana”. [https://www.helius.dev/blog/consensus-on-solana](https://www.helius.dev/blog/consensus-on-solana)  
\[7\] Solana Program Library. “Slashing Program”. [https://github.com/solana-program/slashing](https://github.com/solana-program/slashing)  
\[8\] Solana Documentation. “Programs”. [https://solana.com/docs/core/programs](https://solana.com/docs/core/programs)  
\[9\] Justin Starry. “SIMD-0180: Vote Account Address Keyed Leader Schedule”. [https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0180-vote-account-leader-schedule.md](https://github.com/solana-foundation/solana-improvement-documents/blob/main/proposals/0180-vote-account-leader-schedule.md)  
\[10\] Ashwin Sekar. “SIMD-0212: Slashing”. [https://github.com/solana-foundation/solana-improvement-documents/pull/212](https://github.com/solana-foundation/solana-improvement-documents/pull/212)  
\[11\] Ashwin Sekar and Jon Cinque. “SIMD-0204 Loader Decision”. [https://github.com/solana-foundation/solana-improvement-documents/pull/204#discussion\_r1866733325](https://github.com/solana-foundation/solana-improvement-documents/pull/204#discussion_r1866733325)  
\[12\] Ashwin Sekar. “Solana Foundation Validator Discussion - Dec 12 2024”. [https://youtu.be/7Hmkaj-5QUU?t=709](https://youtu.be/7Hmkaj-5QUU?t=709)  
\[13\] Everstake. “What Is Slashing in Crypto and How Does It Affect You?” [https://everstake.one/blog/what-is-slashing-in-crypto-and-how-does-it-affect-you](https://everstake.one/blog/what-is-slashing-in-crypto-and-how-does-it-affect-you)  
\[14\] Solana Foundation. “09-30-22 Solana Mainnet Beta Outage Report” [https://solana.com/news/09-30-22-solana-mainnet-beta-outage-report](https://solana.com/news/09-30-22-solana-mainnet-beta-outage-report)