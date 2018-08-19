# Yggdrasil Protocol; Trust-minimized Sharding

## A trust-minimized vertically-sharded protocol for THORChain. 

devs@thorchain.org
V0.2 - August 2018

### Abstract 

>Current technology for public blockchains is limited in terms of horizontal scaling. Nodes generally maintain a full copy of the blockchain’s state and transaction history, limiting transaction throughput. Yggdrasil introduces an entirely new vertically sharded mechanism that scales as network saturation increases and validator (called Norne in Yggdrasil) count grows. Validating nodes are known as Nornes in THORChain. 
>THORChain is highly-optimized multi-chain with an efficient consensus algorithm based on practical Byzantine Fault Tolerance (pBFT) in order to provide higher transaction throughput.  
The THORChain multi-chain has `k` number of canonical chains with `k` discrete mem-pools. The network is broken into `n` shards, where each shard is comprised of `c` chains which are deterministically assigned to each shard. On average `c = k / n`. 
>THORChain's Nornes, are split into Norne Sets `NS` of 21 Nornes, the participation `p`, and work to propose and commit blocks using the pBFT consensus algorithm. The total number of Nornes required is set by network saturation, estimated by block size and a benchmarked transaction throughput. Each Norne Set covers `2` randomly assigned shards, known as the Scope. The total number of Shard Pairs equals the total number of Norne Sets, where `NS = nC2`, and deterministic assignment will ensure each shard will be overlapped by a discrete number of Norne Sets. Each Norne Set is appointed as either a validator proposing blocks, or as non-consensus-participating observer. Validators produce blocks in shards they maintain, whilst observers simply observe blocks, watching for fraud. All Nornes are economically incentivised through block rewards.
>As each shard of `c` chains approaches saturation, it is split into two shards, one shard containing `floor(c / 2)` chains and the other with `ceiling(c / 2)` chains, with the absolute minimum being a single chain. Sharding is divergent as well as it convergent; with shards merging low-activity chains to reduce network overheads. Thus the network can scale as required by demand and can potentially be sharded to an upper bound only limited by the number of available Nornes.
>The protocol is optimised for cross-shard trading by ensuring that there will always be a Norne Set validating on any two shards on the two chains containing the atomic trade. Once proposed, any of the watching Sets can observe the atomic trade and post fraud-proofs if fraud is observed. 
>The protocol exhibits two levels of safety; byzantine resistance for each Norne Set and 99% fault tolerance characteristic for at least 1% of all Nornes can post a fraud-proofs on any shard. An on-chain verifiably random function (VRF) nominates the appointments for each shard and can thwart any attempt to control or censor transactions. The VRF is an implementation of the Boneh–Lynn–Shacham (BLS) signature [7] threshold scheme and is a VRF for the network [8]. 
>This protocol employs a novel vertical-sharding approach to solving the scalability trilemma, and exhibits sufficient trust-minimised safety whilst at the same time achieving excellent scalability and decentralisation. 100k transactions per second can be achieved with less 10k nodes and 1m transactions per second can be achieved with less than 80k nodes. 

### Document Set
The following whitepapers should be read in conjunction:

- [THORChain](https://github.com/thorchain/Resources/tree/master/Whitepapers/THORChain) 
A lightning fast decentralised exchange protocol.

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol) 
Secure and fast cross-chain bridges for THORChain.

- [Flash Network](https://github.com/thorchain/Resources/tree/master/Whitepapers/Flash-Network)
A layer 2 Network for instant asset exchange on THORChain.

- [Yggdrasil Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Yggdrasil-Protocol) *(this paper)*
Dynamic multi-set sharding for THORChain.

- [Æsir Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/AEsir-Protocol)
A self-amending forkless consensus algorithm for THORChain. 


## Overview

[Introduction](#introduction)

[Overview](#overview)

[Sharding](#sharding)
  - Blockchain Scalability
  - Sharding Background	
  - Challenges and Trade-offs
  
[Yggdrasil Sharding](#yggdrasil-sharding)
  - Overview
  - Algorithm
  - Cross-chain Transactions
  - Cross-shard Transactions
  - Publishing Fraud Proofs
 
 [Merging and Splitting](#merging-and-splitting)
  - Splitting
  - Merging
  - Alternative: Round Robin

[Analysis](#analysis)
  - Security
  - Performance Estimation	
  - Trade-offs	
  
[Conclusion](#conclusion)	

[References](#references)	

## Introduction

### Overview

Public blockchains traditionally scale very well in terms of the number of nodes supported. However, there generally are severe limitations in terms of transaction throughput scalability. [Ethereum]( https://www.ethereum.org/), for example, supports approximately 15 transactions per second. Considering that all transactions and their associated state changes have to be processed by all nodes, this limitation is not surprising. 

Poor blockchain scalability in terms of transaction throughput is probably one of the two most limiting factors holding back the development of large-scale decentralized applications. The second factor, transaction cost, is related to transaction throughput, as cost should go down once transactions are not competing for scarce bandwidth.

THORChain is a blockchain purposely built with scalability in mind and implements several measures to guarantee high transaction throughput. The main use case targeted by THORChain is the implementation of a decentralized cryptocurrency exchange.
The platform architecture is based on a multi-chain solution, allowing multiple sidechains to settle on a root chain, called MerkleChain.  MerkleChain stores the root hashes of the sides chains’ transactions [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree ). Each sidechain represents a token, with a separate mempool. However, as all token chains share the same global address space, inter-side-chain communication is trivial, and transactions can always be found.

Consensus in THORChain is achieved using a delegated Proof of Stake protocol with a target of 21 Nornes per Norne Set that reaches consensus using a [pBFT voting protocol](http://pmg.csail.mit.edu/papers/osdi99.pdf ) [1]. Nornes are chosen according to the number of coins staked. Built-in reward and penalty mechanisms guarantee correct Norne behavior at the protocol level. Malicious actors can also be evicted and replaced by a node from a queue of backup Nornes.

The focus of this paper is on sharding. Traditionally this method of scaling has received little attention due to its complexity and trustful nature of cross-shard transfers. Currently, only Ethereum and Cosmos appear to be pursuing similar solutions. That said, sharding is the only base-layer scaling solution which reduces the requirements of individual Nornes. The trust-full nature of sharding is that in cross-chain transfers it is not possible to be sure that the crediting and debiting of funds is done appropriately without validating other shards. While it is always safe to send tokens to another shard (worst case scenario is they don't credit it), receiving tokens from another shard risks inflating the overall money supply if they have not properly debited the sent tokens on their shard. In order to completely minimize trust, each Norne must be responsible for every shard. Of course, this removes the benefits of sharding entirely. 

We propose a solution that is a better balance between the fundamental trade-offs in sharding solutions. In what remains of this paper, we will provide some further background on blockchain scalability and sharding, before detailing THORChain’s approach. 

## Sharding 
### Blockchain Scalability
Scaling is perhaps the most pressing concern for current blockchain technologies. Scalability involves a number of trade-offs. Ethereum’s co-founder Vitalik Buterin describes the scalability trade-off as a [trilemma](https://github.com/ethereum/wiki/wiki/Sharding-FAQ) between scalability, security, and decentralization (Figure 1). This basically means, that in order to optimize a blockchain for scalability, either security or decentralization needs to be relaxed.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/scalability-trilemma.png" width="400" height="215" />

_Figure 1 : Scalability Trilemma_ 

This can be seen in third-generation blockchains focusing on scalability, such as [EOS](https://eos.io/ ) reducing the number of Nornes to 21, thereby introducing a risk of centralization. 

Scaling proposals can be classified into two categories:
1) Off-chain scaling consists in second-layer solutions that execute a number of transactions off the blockchain and use the blockchain's ledger for occasional settlement. By doing so, the requirement for sequential consistency [2] is relaxed. Bitcoin’s [Lightning](https://lightning.network/ ) and Ethereum’s [Raiden Network](https://raiden.network/ ) fall into this category.
2) On-chain scaling solutions may entail modifying the consensus protocol, such as THORChain’s own model, in which a number of Nornes are chosen from a pool of staking nodes to execute an efficient variant of the Practical Byzantine Fault Tolerance [3] protocol to greatly improve transaction throughput and achieve very low-latency block finality. Other on-chain approaches include tweaking certain blockchain parameters, for example, Bitcoin Cash’s block size increase.

In between the two extremes are solutions that split up the blockchain into manageable pieces, in order to reduce the load on individual nodes. This split can be done vertically along application specific divides, as in the side-chain approach ([Lisk](https://lisk.io/), [Plasma](https://plasma.io/) [4]) or [THORChain’s multi-chain solution](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md). It can also be done horizontally, through sharding. 

### Sharding Background
Sharding is a technique common in distributed data management systems. In databases, tables are split horizontally across rows into different shards, or vertically into columns. Different shards are stored by different nodes to spread the load. Horizontally sharding is more secure, but less optimised due to database lookups. 
Sharding proposals for blockchains are similar in nature. The basic idea is dividing either transaction processing, global state or both into shards maintained by different sets of Nornes. A set of root Nornes typically synch between shards. This process is illustrated in Figure 2.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/merkletrie.png" width="400" height="215" />

_Figure 2: Sharding Concept_

One of the first sharding proposals for modern blockchain was ELASTICO [5]. Similar approaches, such as early sharding proposals for Bitcoin based on [Merklix trees](https://www.deadalnix.me/2016/11/06/using-merklix-tree-to-shard-block-validation/ )  and [Zillqua](https://docs.zilliqa.com/whitepaper.pdf ) share the same property of only focusing on one of the two components: state or transactions. This means they either improve transaction throughput or a node’s storage requirements, but not both. 
[Ethereum’s sharding solution](https://github.com/ethereum/wiki/wiki/Sharding-FAQs ) is still in active research. However, the goal of Ethereum sharding is to shard state, as well as transaction processing. 
Splitting up a Blockchain into different shards requires a more hierarchical approach. A number of individual chains are created, one for each shard. In order to maintain the overall chain, these shards need to somehow connect to the main chain. 
Ethereum introduces a hierarchy of four node types for this purpose:
1. Super full-nodes maintain every collation and the main chain. They also integrate collations from different shards into main chain blocks.
2. Top-level nodes process the main chain and give access to all shards.
3. Single-shard-nodes are the same as top-level nodes, but also maintain all the collations of their particular shard.
4. Light nodes only maintain block headers from the main chain but can request state from different shards when required.

In a sharded system following this model, blocks are valid when the transactions in all included collations are valid. Additionally, all collations need to be signed by a certain percentage of collators, typically two thirds.

### Challenges and Trade-offs
#### Single-Shard Takeover Attack
A problem in sharded blockchains is that each shard is now maintained by a much smaller number of nodes than the whole chain. It is thus theoretically much easier for an attacker to get hold of a sufficient majority in a single shard to manipulate data.
This problem is known as the `1%` attack, based on the assumption that in a `100` shard system it takes [1% of the networks](https://cosmos.network/whitepaper#appendix ) hash rate to dominate the shard.
This problem can be mitigated by choosing Nornes for shards through random sampling and changing this sampling frequently. 
Choosing and changing collators randomly is much easier in proof of stake-based systems, as collator nodes can just be randomly sampled from the set of Nornes that participate in staking. 

#### Cross-Shard Communication
Communication between shards has to be performed via the main chain. The difficulty, in this case, is to maintain the atomicity property of transactions.
Let’s consider this issue focusing on digital assets, such as a token. Sharding must have a protocol for shards to interact and swap tokens. A simple protocol for two shards to send tokens amongst themselves can be thought of as a credit-debit system. The sending shard debits a cross-shard payment while the receiving shard credits the sending shard new tokens.
The fundamental problem with sharding is that with non-overlapping Norne sets is for the receiving shard to be sure the sending shard properly froze the debited funds.  For such a system to work each shard must have some basis for trusting other sending shards to properly debit cross-shard transfers. While this is not trust-minimizing, with public blockchains it is easy to tell if there has been a violation provided that copies of the shards exist independent from the Nornes, who themselves could cheat.

It might see that this limitation could be circumvented if a single Norne set was responsible for all shards. The issue here is that in such a case the benefits of sharding are voided. If one Norne set is overlooking all shards, they might as well be validating a single chain instead, since the computational requirements are approximately the same.

### Performance Issues
Increasing the number of block producing nodes in a consensus-threshold PoS based algorithm reduces the performance of the network as node count grows. This is especially true for pBFT based algorithms where finality is achieved prior to block being committed. Requiring a higher number of validators to agree on a block will increase time-to-finaliy with a higher node count. Additionally, the signature aggregation of high numbers of nodes increase blockchain bloat. Indeed, incorporating Schnorr and BLS signature schemes reduce signature aggregation sizes down substantially; however does not improve network finality times; an important consideration for a high-throughput chain. A solution could be to cap block production on each shard to a fixed number of validators and incorporate a round-robin approach. The benefit to this is that finality would be close to optimal and signatures would be a fixed and predictable size. This approach comes with a sacrifice in decentralisation as less validators proportionally are required to agree on blocks in each shard as the shard count grows. 

## Yggdrasil Sharding
### Overview
The Yggdrasil sharding solution is a vertical sharding solution that shards down entire chains, rather than splitting up chains. Thus, the protocol uses sharding techniques to implement a multi-chain solution, rather than retroactively fitting sharding to an existing chain. 

The benefit to this is it does not require a coordinator to piece together shards, and it minimises cross-shard communication as transactions are more likely to be asset movement in a single shard. Splitting up chains in THORChain is trivial as each asset maintains its own tokenChain and the address space allows for sub-divisioning.

Yggdrasil requires that each of the Norne Sets cover only fixed number of shards, the Scope, to manage network overhead and achieve a high level of trust-minimization for cross-shard transactions. Yggdrasil enforces a minimum number of Nornes per shard pair; that is any two shards have a minimum number of Nornes proposing blocks on them and facilitating cross-shard transactions. By design, every shard will have a relationship with every other shard through a common set of Nornes. 
For this reason, we describe the Yggdrasil sharding solution as trust-minimized sharding. 

Yggdrasil is built this way for one cornerstone feature; to facilitate cross-chain transactions for cross-chain token transactions and trading. Cross-chain transactions can either be internal to shards, or across two shards. This is due to chains being spawned inside of an existing shard first, before being split into their own shard when the network demands for it.

The following are the specific use cases:

|**Use Case**|**Description**|**Mechanics**|
|:---|:---|:---|
|Swapping Token 1 (T1) with Rune via the T1 CLP (or vice versa).|Alice sends Token 1 (T1) into the CLP on the T1 chain. Rune (T0) is emitted to Alice’s Rune account on the T0 chain.|`T1xAlice bal(T1) -> T1x0000` `T1x0000 bal(T0) -> T0xAlice`|
|Swapping Token 1 (T1) with Token 2 (T2) via the T1 and T2 CLP (or vice versa).|Alice sends Token 1 (T1) into the CLP on the T1 chain. Rune (T0) is emitted to T2 CLP on the T2 chain. Token 2 is emitted to Alice’s T2 account on the T2 chain.|`T1xAlice bal(T1) -> T1x0000` `T1x0000 bal(T0) -> T2x0000` `T2x0000 bal(T2) -> T2xAlice`|
|Trading Token 1 (T1) for Rune (T0) on the order book (or vice versa).|Bob creates a T0:T1 market sell order. Alice broadcasts a T0:T1 buy order into the mem-pool for T0 and T1. Bob’s T0 is traded for Alice’s T1. Bob receives T1 and Alice receives T0.| `T0xBob bal(T0) -> T0xBobSell` `T1xAlice bal(T1) -> T0T1memPool` `T0xBobSell bal(T0) -> T0xAlice` `T1xAlice bal(T1) -> T1xBob`|
|Trading Token 1 (T1) for Token 2 (T2) on the order book (or vice versa).| Bob creates a T1:T2 market sell order. Alice broadcasts a T1:T2 buy order into the mem-pool for T1 and T2. Bob’s T1 is traded for Alice’s T2. Bob receives T2 and Alice receives T1.|`T1xBob bal(T1) -> T1xBobSell` `T2xAlice bal(T2) -> T1T2memPool` `T1xBobSell bal(T1) -> T1xAlice` `T2xAlice bal(T2) -> T2xBob`|

Each transaction must be atomic; where the entire transaction will proceed or fail. As trading involves two separate blocks on two separate chains, the proposer must have awareness of both chains before proposing the transaction. This is why the Yggdrasil protocol guarantees that there is always a group of Nornes that are available to propose blocks on any given shard pair. 
 
### Algorithm
In THORChain each Norne is randomly allocated exactly two distinct shards, and the number of shards in the system is defined as `N`, which is set by network saturation (defined below). The total number of Nornes must be equal to the total number of combinations of pairs of shards, `N choose 2`, multiplied by the minimum number of Nornes per Shard, `p`, set at 21. As an example, take 4 shards having `4C2 = 6` unique pairs of shards, which requires `4 * 21 = 126` Nornes in total. Each shard will have `(N-1 * 21) = 63` Nornes syncing, validating and observing all blocks occurring on all chains in the shard in six different Norne Sets.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure1.png" width="350" height="250" />

*Figure: 6 Sets maintain 4 shards. For a cross-shard transaction from ShardA to ShardB, there is 1 Set that has full clarity and 4 sets that have partial clarity on the trade.*

Each shard shares a common set of Nornes with every other shard. Take shard `S_a` and `S_b` with total shards `N`. There will be 21 Nornes that maintain shards `S_a + S_b`, `(N-2) * 21` that maintain `S_a + S_k` for `k = 0, ..., 4 where k != a and k != b`, and `(N-2) * 21` that maintain `S_k + S_b` for `k = 0, ..., 4 where k != a and k != b`. This can be simplified to 21 `S_a + S_b` and  `(N-1) * 21` `!(S_a + S_b)`. 
The network then deterministically begins a round-robin block production, with alternatively one Set proposing and committing blocks whilst the other Sets watch. Each Set produces blocks for all chains in both shards, so from the perspective of the Set proposing blocks for the shard pair, the two shards are completely homogenous. The following is an indication of this mechanism. The 67% threshold `15 of 21` consensus applies to the Set and the particular Norne that proposes the block is randomly chosen. As the Set is byzantine resistant, applying a second layer of random assignment to a Norne Set to produce on a shard pair may be unnecessary. Instead the protocol could determine the particular round-robin sequence of Norne Sets deterministically. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure2.png" width="350" height="250" />

*Figure: Round 1. Set 1 and Set 6 produce blocks for Shards A, B, C and D. Single-chain, Cross-chain Intra-shard and Cross-shard transactions are collected and committed on all chains in all shards.* 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure3.png" width="350" height="250" />

*Figure: Round 2. Set 2 and Set 4 produce blocks for Shards A, B, C and D. All transactions are collected and committed on all chains in all shards.* 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure4.png" width="350" height="250" />

*Figure: Round 3. Set 3 and Set 5 produce blocks for Shards A, B, C and D. All transactions are collected and committed on all chains in all shards.* 

### Single-chain Transactions
Alice is on `Chain1` on `ShardA`. She makes a transaction to send `Token1` to another address:

- Her transaction is broadcast into the mempool of ShardA with the transaction ID. `ID: T1xa1 -> T1xa2`
- A nominated Norne collects the transaction and proposes it `ID:T1π`.
- A super-majority of the Set (15 Nornes) then agree and commit the transaction to `Chain1` in `ShardA`. 
- The rest of the Nornes sync and validate the transaction. 

Assuming the Proposing Set is not cartel-controlled or DOS’d, all transactions in the MemPool will be proposed. If it is cartel-controlled, the most they can do is censor transactions. Any of the observing nornes from watching Sets can identify and report this for on-chain governance action, which may include replacing the Set with other Nornes. 

### Cross-chain Transactions
Alice is on `Chain1` on `ShardA`. She makes a trade with Bob for `Token2` on `Chain2` in `ShardA`: 

- Her transaction is broadcast into the mempool of  `ShardA` with the transaction ID. `ID: T1xa -> T1xb; T2xb -> T2xa; `
- A proposing Norne collects the transaction and proposes `ID:T1π(T2π)` and `ID:T2π(T1π)` as two blocks; one for each `Chain1` and `Chain2`, publishing hashes of each block in the other to ensure atomicity.
- A super-majority of the Set (15 Nornes) then agree and commit both blocks to `Chain1` and `Chain2` in `ShardA`, after ensuring the signatures of all blocks, as well as the trade math, are valid.
- The rest of the Sets sync and validate the transaction. 

A rogue norne (friends with Bob), may attempt to publish a non-atomic trade; where only Token1 move, but Token2 does not; ie, Bob gets both Token1 and Token2. He would do so by publishing  `ID:T1π(T2-)` and `ID:T2-(T1π)`. All nornes in the Set have a copy of the original order can immediately identify the fraud and not commit the transaction. 

If the Set is cartel-controlled (more than 15) then they can publish and commit the fraudulent block. However, any of the other nornes in the proposing Set and all of the nornes in the observing Sets have a copy of the original order and state; so can publish a fraud proof on all nornes that published the fraud. 

### Cross-shard Transactions
Alice is on `Chain1` on `ShardA`. She makes a trade with Bob for `Token4` on `Chain4` on `ShardB`:

- Her transaction is broadcast into the mempool of `ShardA` and `ShardB` with the transaction ID. `ID: T1xa -> T1xb; T4xb -> T4xa; `
- A Common Norne collects the transaction and proposes `ID:T1π(T4π)` and `ID:T4π(T1π)` as two blocks; one for each `Chain1` and `Chain4`, publishing hashes of each block in the other to ensure atomicity. 
- A super-majority of the Common (15 Nornes) then agree and commit both blocks to `Chain1` and `Chain4` in `ShardA` and `ShardB` respectively, after ensuring the signatures of all blocks, as well as the trade math, are valid.
- All Nornes in `ShardA` observe a valid `ID:T1π(T4π)` transaction, containing correct asset movement for `T1` (from Alice to Bob). They are not aware of the `T4` chain so cannot validate the `T4` transaction. 
- All Nornes in `ShardB` observe a valid `ID:T4π(T1π)` transaction, containing correct asset movement for `Tb` (from Bob to Alice). They are not aware of the `T1` chain so cannot validate the `T1` transaction. 

A rogue validator (friends with Bob), may attempt to publish a non-atomic trade; where only Token1 move, but Token4 does not; ie, Bob gets both Token1 and Token4. He would do so by publishing  `ID:T1π(T4-)` and `ID:T4-(T1π)`. All Nornes have a copy of the original order can immediately identify the fraud and not commit the transaction. 

If the Set is cartel-controlled (more than 15) then they can publish and commit the fraudulent block. However, the fraudulent block will be not valid for either `ShardA` or `ShardB` so Nornes from a defrauded Shard will reject the block and publish a fraud proof. They can do this because they have a copy of the original signed order by the user can demonstrably show the fraud. 

In a cross-shard trade, there will always be a Set of 21 Nornes observing the transaction and available to make the atomic transaction. The trade is safe as it requires a minimum of `15 of 21` Nornes to collect, propose and commit the blocks containing the trade. Given that only a Norne inside the overlapping Sets can propose the transaction, and there will be `*(n-1)C2 * 21) * 2 - 21` Nornes who will be watching either one of the Shards and able to make a fraud-proof.

### Publishing Fraud-proofs
Observing Nornes can deterministically identify fraud and have the incentives to publish any detected fraud to earn the slashed stake from the fraudulent Norne. Consider the case where a cartel-controlled group of Nornes publish and commit a non-atomic trade `ID:T1π (T4-)` and `ID:T4- (T1π)`, where `T1` is on `ShardA` and `T4` is on `ShardB`. All of the `ShardA` Nornes will not be able to identify the fraud as they are not aware of the state of `ShardB`; and the transaction `ID:T1π (T4-)` fulfils all transaction criteria on `ShardA`. `ShardB` Nornes however can inspect the signed transaction they have from their memPool with that that was published to the chain in order to determine the fraud by comparing transaction IDs. If the transaction ID from the memPool does not match the asset movement of chains; then there was fraud. 

### Prioritising Cross-chain Transactions
As the  number of shards increase, the ratio between common Nornes `S_a & S_b` and non-common Nornes between two shards decreases. With 4 shards the ratio is 33%, with 10 shards the ratio is 11%. The result of this is that cross-shard transactions are less likely to be picked up from the common memPool. Nornes are encouraged to counter this development by economic incentives as the cross-shard transaction involves gas fees from two chains and the proposing Norne is set to earn twice as much. Thus, Nornes will prioritize cross-chain transactions.

### Cross-shard delays
The main benefit to the round-robin approach is that finality would be close to optimal (indeed 500ms is achievable) and signatures would be almost negligible. The main downside from a performance perspective is that cross-shard trade (even if prioritised through higher fees) would be probabilistically produced in the round-robin, increasing the time that they are confirmed in. 

As an example, in a round-robin of 31 shards; a cross-shard trade involving a shard pair may take up to 30 blocks before it would be produced; so the time to confirmation and finality is of range: `L * 2  :  L * 2 + 30 * L * 2`, where `L` is block latency. With 0.5 second blocks this could be 0.5 seconds to 16 seconds. Since the round-robin is deterministic, the approximate time to finality for any given trade could be indicated ahead of time to the user. 

Of note; this only affects cross-shard trades; not intra-shard cross-chain trades; which would be produced on time every 0.5 seconds by a Norne Set in the network. The probability of a trade being intra-shard and not cross-shard is `c-1 * n / (c*n Choose 2)`, where `c` is the average number of chains per shard and `n` is the total number of shards. Thus at 31 the likelihood of cross-shard over 98%. In reality this means that a cross-shard trades will be produced with 98% likelihood, and they are 100% likely to be produced inside of 16 seconds and 3% `1 / 31` likely of being produced in less than 1 second. In summary, for our benchmark of 31 shards, 10,000 Nornes and 155k TPS; there is a maximum of 16 seconds for a cross-shard transaction, with the typical trade being processed in 8 seconds. 

## Merging and Splitting Shards

### Splitting Shards

The protocol starts off with a single chain in a single shard, covered by a cap of `21` Nornes in a single Set. It is assumed there is a queue of valid and waiting Nornes that are keen to enter the Set. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure5.png" width="350" height="160" />
*Figure: A single shard with a single chain.*

As tokens are created, discrete chains of number `c` are added to the shard. To prepare for a possible split the cap of Nornes increase from `21` to `21 * 2 = 42` as network saturation climbs from 10% to 90%. Queued Nornes enter the Set automatically.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure6.png" width="350" height="160" />

*Figure: A single shard with multiple chains.*

As soon as the network reaches 90% saturation for more than 100 blocks, the shard of `c` chains split into two shards; one shard containing `floor(c / 2)` chains and the other with `ceiling(c / 2)` chains, administered by two Sets of 21 Nornes, with 42 common Nornes (complete overlap). The exact split is random; but may be biased towards chains that transact with each other frequently, ensuring the cross-chain transfers are minimised. As all 42 Nornes were split from a Set which had been syncing all chains in the shard the split will be seamless.

The two sets begin an alternating block production schedule, with each Set producing blocks for all chains on both shards and observing all blocks produced. Thus the protocol has the performance of a single 21 node validator set producing blocks on `c` chains, but the security of 42 nodes. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure7.png" width="350" height="225" />

*Figure: Round 1 on 2 shards; Set 1 produces blocks for both chains.*

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure8.png" width="350" height="225" />

*Figure: Round 2 on 2 shards; Set 2 produces blocks for both chains.*

| Set | Shard Relationship
|---|---|
| 1 | Maintains ShardA, Watches ShardB. 
| 2 | Watches ShardA, Maintains ShardB. 

Importantly, the Sets are now syncing across two shards with chains; but propose and commit blocks to both shards. From their observation and action; nothing has changed. The MerkleChain keeps track of Set allocations. 

Once again, as saturation in one shard (ShardA) climbs to 90%, ShardA's Norne cap increases from `21` to `21 * 2 = 42`, and then splits in the same manner. The new ShardC has half of ShardA’s pre-split chains. Set1 prunes all of ShardC’s chains, Set 2 prunes ShardA, and Set 3 retains ShardA and ShardC chains. Once again, nothing fundamentally changes to the protocol, apart from the MerkleChain notarising the allocations, and two sets (1&2) pruning chains. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure9.png" width="350" height="275" />

*Figure: Splitting to three shards is seamless and asynchronous.*

| Set | Shard
|---|---|
| 1 | Prunes ShardC, Spawns Set3.
| 2 | Prunes ShardA. 
| 3 | Watches ShardA, Maintains ShardC.

The network then engages in a round robin of `Set1:A&B; Set3:C` `Set2:B&C; Set3:A` and then `Set1:B; Set3:B&C`

As saturation in one shard (ShardA) again climbs to 90%, Set 1 increases its Norne cap from `21` to `21 * 2 = 42`, and then prepares to split in the same manner. The split happens in a concerted and deterministic method involving pruning and retaining the chains. Once the Set has split then there immediately becomes available two additional sets that can be spawned from Set 2 and Set 3 The three new Sets inherit shard relationships as per the following table: 

| Set | Shard
|---|---|
| 1 | Prunes ShardD, Spawns Set 4.
| 2 | No Change, Spawns Set 5. 
| 3 | Prunes ShardD, Spawns Set 6.
| 4 | Prunes ShardB, Watches ShardA, Maintains ShardD.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure10.png" width="350" height="260" />

*Figure: Splitting to three shards is seamless and asynchronous.*

Once the ShardD is established, two more Sets of 21 Nornes open up for ShardB, ShardC and ShardD. 42 more Nornes sync the Shards and auction their way into Set 5 and Set 6. This can happen sequentially or concurrently to the organization above. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure11.png" width="350" height="250" />

*Figure: The network has 6 Sets alternating to produce blocks for `c * 6` chains in 6 Shards.*

| Set | Shard
|---|---|
| 5 | Syncs & Watches  ShardB, Syncs & Watches ShardD.
| 6 | Syncs & Watches ShardC, Syncs & Watches ShardD.

This process continues in sequence for more splitting of shards. The following is indicative of the first 10 Shards. The mechanics for the split is that the splitting shard spawns the new Set; and once split, `nC2 - ( ( n-1 )C2 ) * 21` more Norne positions open up to watch the new shards. 
 
|Shards|Pairs `nC2`|Nornes per Shard|New Nornes|Watching Nodes|Total Nornes (21 per pair)|
|:---:|:---:|:---:|:---:|:---:|:---:|
|1|1|21|-|0|21|
|2|1|21|21|21|42|
|3|3|42|21|21|63|
|4|6|63|63|42|126|
|5|10|84|84|63|210|
|6|15|105|105|84|315|
|7|21|126|126|105|441|
|8|28|147|147|126|588|
|9|36|168|168|147|756|
|10|45|189|189|168|945|


*Note 1: A shard can only be reduced to a minimum of a single chain to prevent splitting the state of a single chain; which is very complex.*

*Note 2: Flexxing the cap on Nornes does not change performance; it simply prevents a shard being split but without the minimum number of Nornes available to maintain.*

### Merging Shards
Shards can also merge if shard saturation becomes minimal, defined as less than 10% saturation for more than `x` blocks (100). The protocol incentive is that by merging two shards, a Set will earn more in transaction fees from the two merged shards, so will attempt to be the Set that coordinates a merge and gain from combining two other shards. A shard cannot start a merge process until it is less than 10% saturation and a shard cannot be merged to another that has more than 90% saturation. As saturation levels are tracked on the central MerkleChain this leads to deterministic merging. 

Each shard is maintained by `N-1` Sets, so for every merge, there will be a race condition where any Set that maintains the min activity shard can attempt to sync and coordinate the merge in a winner-take-all approach. The winner will be the first to prove they have found, synced and merged two low activity shards satisfying all rules with at least 67% support from their Set. To complete a faster sync, Sets will find the smallest and lowest activity shards to speed up the process. Once a Set broadcasts to the MerkleChain that they have synced and merged two low activity shards, the Merklechain will shuffle Sets to favour the winning Set. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure12.png" width="350" height="260" />

*Figure: Sets compete to merge shards by being the fastest to sync shard pairs.*

In the example above, ShardB is the minimal activity shard, and is maintained by Set 1, Set 2 and Set 5. All Sets immediately ping the DHT to identify another low-activity shard (less than 90%) and then sync that shard for a merge; here it is ShardC. Sets must be syncing the two shards that are to be merged, as well as a third that will be the final shard pair that is not ShardB or ShardC. 

|Set|Merging Shards|Shard Pair|Action|
|:---:|---|---|---|
|Set 1| `ShardB & ShardC`| `ShardA or ShardD`|Sync ShardC|
|Set 2| `ShardB & ShardC`| `ShardA or ShardD`|Sync ShardD|
|Set 5| `ShardB & ShardC`| `ShardA or ShardD`|Sync ShardC|

In this example Set 2 manages to sync ShardD and complete the merge on ShardB and ShardC the fastest, broadcasting to the MerkleChain the success. This may be likely since to them ShardB and ShardC were already homogenous (as a pair) and they simply had to sync ShardD. For `x` blocks they signal a fully synced ShardD and provided ShardB still remains below 10% saturation then they can complete the merge. 

At this point there are 3 unnecessary Sets that share commonality. They are Set3 sharing commonality with Set1, Set5 sharing commonality with Set2 and Set6 sharing commonality with Set2. These three sets can deterministically removed based on stake levels:

|Set Redundancy|Action|Reason|
|:---:|---|---|
|Set 1 and Set 3| Demote Set 3|`Set 1 Stake > Set 3 Stake`|
|Set 2 and Set 5| Demote Set 5|`Set 2 Stake > Set 5 Stake`|
|Set 2 and Set 6| Demote Set 6|`Set 2 Stake > Set 6 Stake`|

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Yggdrasil-Protocol/images/figure13.png" width="350" height="260" />

*Figure: Sets compete to merge shards by being the fastest to sync shard pairs.*


Once demoted, there are 3 Sets, 3 Shards and 63 total Nornes actively producing blocks. The 63 demoted Nornes have various Shards and Chains synced, so can be made available to immediately cater for a possible new split back to 4 shards again. This is an effective way to re-org two shards that have large differences in activity levels, such as one being less than 10% and the other being close to 90%, in such a way that after 200 blocks the two shards will have 50% distributed each. The Split should immediately happen after the Merge so there is no cost to Nornes as the re-org will happen seamlessly.  

The following is the sequence of a Split and subsequent Merge:

|Seq|Event|Action
|:---:|:---:|:---|
|1| - | Norne Set (Set1) of 21 Nornes, maintains shard `S_A` with `c` chains 
|2|`0->90% Saturation` | Set1 Cap increases to 42 Nornes
|3|`>90% Saturation` | Norne Set (Set1) start signalling to split
|4|`100 Blocks`
|5|`101st Block`| A new Norne Set (Set2) commissioned maintaining a new shard `S_B`. Set1 watches `S_B` and Set2 watches `S_A`. Both Sets have 21 Nornes. 
|6|`45->10% Saturation on `S_B` |  
|7|`<10% Saturation on `S_B` |  Set1 and Set2 ensure they are synced and signal a merge. 
|8|`100 Blocks`
|9|`101st Block`| Set2 completes the merge.

_Table: Order for splitting and merging_

With this mechanism the Protocol can scale up and down depending on saturation, with the correct amount of Nornes and Norne Sets at all times. 


### Estimating Saturation
Tracking saturation is now a huge part of how the Yggdrasil Protocol works. This can be done by each Norne calculating saturation on each shard they maintain and publishing this into the MerkleChain. Saturation in blockchains is a function of block producers finding the most valuable transactions that fit under a block size limit; which is NP-complete problem where there is no efficient solution to find in the first place. Indeed tracking block sizes is not direct answer of saturation as transaction types have different weights. 

As such there needs to be clear definition of blockchain throughput, and thus saturation, to identify a shard that is saturated and should split (or not and should merge). We propose using both a function of block size as a proportion of block limit, caveated by a benchmark of recorded all-time-high transaction throughput. The rule proposed is 90% block limit and 90% ATH TPS. 

As an example; if there a `10` chains in a shard, `S_a`, and the block limit is 4mb per chain, then the shard is 90% saturated at `10 * 4 * 0.9 = 36mb`. If the highest recorded TPS in a Shard is 5000 TPS, then the 90% threshold is 4500 TPS. A shard will then split above 36mb and 4500 TPS for more than `x` (100) blocks. 

## Analysis

### Performance Estimation
THORChain scales with the number of Nornes, but this is dependent on actual and sustained network demand to prevent overheads being added to the network. To conduct an analysis on the protocol, the scenario of hosting 1800 tokens and their chains are explored (current tradable tokens on [CoinMarketCap](coinmarketcap.com)) with a maximum of 10,000 Nornes available. Bitcoin has over 100k nodes, whilst Ethereum has 25,000 nodes, so this is seen as conservative. 

**Parameters**

| Parameter | Value | Notes|
|---|---|---|
| Tokens | 1800 | Current tradable tokens on CoinMarketCap.
| Chains | 1800 | Each token has its own chain.
| Chains per Shard | 58 | Average number of chains in each shard without exceeding 90% saturation.
| Total Shards | 31 | Chains / Chains per Shard.
| Scope | 2 | The number of Shards each Set must cover.
| Threshold per Set | 21 |  Arbitrary but safe number (lowest is 4 for byzantine resistance).
| Sets | 465 | NC2 `31 Choose 2`
| Minimum Nornes | 9765 | Minimum to maintain 10% saturated network.
| Nornes per Shard | 630 | Total number of Nornes syncing each Shard, with 1 Set Primary. 
| Nornes watching each Shard | 504 | Nornes watching each Shard, with 30 Secondary Sets. 
| TPS per Set | 10,000 | Tested maximum of PBFT for less than 1000 nodes.
| TPS per Shard | 5,000 | Each Set covers 2 shards.
| Network TPS | 155,000.00 | Theoretical performance for 31 Shards. 

As can be seen, assuming the the number of nodes required are available, the network needs less than 10,000 Nornes to achieve over 150k TPS for 1800 Tokens on 31 shards. The key number is the byzantine resistant threshold for each Set; set at 21. 21 is seen as a safe (but centralised) threshold in the industry; and in the case that the protocol only has one shard (low use when first starting), there will only be one Set of 21 maintaining network security. 

There is an optimisation that could see the Threshold reduce to as low as 7 after there are sufficient total Nornes (such as 100). In this case the network could achieve 265k TPS (a 70% improvement) with only 9646 Nornes required, with all other things held constant. In this case each shard would be maintained by 7 Nornes, but watched by 350 Nornes which is safe.

To achieve 1m TPS, there would need to be either 79,600 Nornes in total with 200 shards at 4 Nornes per Set (low security) or 139,300 Nornes with 7 Nornes per set. These are not inconceivable numbers with the right incentivisation. 

**Comparison.** 

The first cryptocurrency known to use masternodes was Dash [6] of which there are currently `4000` nodes. With this many bonded Nornes, the Yggdrasil protocol algorithm could maintain `20` shards with byzantine resistance achieved. Since each Norne need only validate two shards, this means bandwidth, space, and computational requirements are reduced by up to 95%.

### Security
The main challenge with the Yggdrasil sharding approach is that the number of Nornes that maintain each shard reduce proportionally to the total as the number of shards increase. As the number of shards grows, a much smaller number than two thirds of all Nornes need to be compromised or form a cartel to perform an attack on the shard. Indeed the most valuable chains need far more security than chains that maintain low-value tokens, and once such chain is the RuneChain `T0`.
The following are unique aspects of the duties for any Norne that maintains the RuneChain in one of the 2 Sets of the RuneChain Validating Group:
- Maintain the MerkleChain, which syncs the network, holds the verifiably random function and the DHT for the THORChain Name Service. 
- Maintain the RuneChain and process all CLP transactions across all tokenChains. 
- Maintain the staked deposits for each Norne, Staker and Delegator. 
- Maintain and coordinate the Æsir protocol on-chain governance. 
- Maintain the Bifröst Protocol. 
- Coordinate the Flash Network. 

 The following are strategies to counter this vulnerability:

1)  Economic incentives to encourage Nornes with the largest stake to secure the most valuable tokenChains. The Rune Chain and the Bitcoin Chain may well be the most valuable in the Network, so should require the highest economic security. Transaction fees collected on each chain will be roughly proportional to the value of the chain and the number of transactions. These fees are only paid to the Set that maintains it. As such, waiting Nornes will most likely queue to enter the Sets for the most valuable chains. 
2) On-chain Verifiably Random Function nominates the shards that each set maintains. 
3) Any compromised shard can be isolated from other shards by denying incoming transfers. Furthermore, it is possible to eject malicious Nornes if a majority of the overall Nornes is obtained. Interestingly, as the number of shards grows the storage, bandwidth, and computational requirements remain constant for individual Nornes.

The advantage of the Yggdrasil protocol lies in its simplicity. The protocol is easy to understand and analyze, whilst providing increased cross-shard security. Furthermore, in contrast to many existing sharding protocol, our protocol shards both transactions and state. 

Our scheme provides a balance between decentralization, security, and trust-minimization, optimizing the above-mentioned scalability trilemma. Decentralization comes with constant storage, bandwidth, and computational requirements regardless, of the number of shards. As the number of shards grows, so must the number of Nornes. Security here is clearly less than that of a single blockchain but there are mitigation strategies if a shard becomes defective. 

Finally, this scheme far less trust-full than systems, such as EOS, as the number of Nornes contributing to consensus is much higher.

## Conclusion
We have presented the THORChain sharding solution, a novel multi-sharding solution suitable for high transaction throughout blockchains. Out approach optimizes the tradeoffs involved in sharding by covering each shard with multiple Norne sets. Security of cross-shard transfers is preserved by allowing observing nodes to post fraud-proofs to prevent malicious behaviour. 

We have shown how the number of Norne sets required increases with the total number of shards in existence, yet the performance of each shard remains constant. Finality for single chain transactions remains close to theoretical minimum, however a probablistic delay affects cross-shard transactions. 

## References

**[1]** Miguel Castro and Barbara Liskov. 1999. Practical Byzantine fault tolerance. In Proceedings of the third symposium on Operating systems design and implementation (OSDI '99). USENIX Association, Berkeley, CA, USA, 173-186. http://pmg.csail.mit.edu/papers/osdi99.pdf 

**[2]** Lamport, L. (1979). How to make a multiprocessor computer that correctly
executes multiprocess programs. IEEE Trans. Computers, 28(9):690- 691.

**[3]** Miguel Castro and Barbara Liskov. 1999. Practical Byzantine fault tolerance. In Proceedings of the third symposium on Operating systems design and implementation (OSDI '99). USENIX Association, Berkeley, CA, USA, 173-186

**[4]** Plasma: Scalable Autonomous Smart Contracts. Joseph Poon and Vitalik Buterin. 2017. https://plasma.io/plasma.pdf 

**[5]** Loi Luu, Viswesh Narayanan, Chaodong Zheng, Kunal Baweja, Seth Gilbert, and Prateek Saxena. 2016. A Secure Sharding Protocol For Open Blockchains. In Proceedings of the 2016 ACM SIGSAC Conference on Computer and Communications Security (CCS '16). ACM, New York, NY, USA, 17-30. DOI: https://doi.org/10.1145/2976749.2978389

**[6]** Evan Duffield, Daniel Diaz. Dash: A Privacy-Centric Crypto-Currency. https://github.com/dashpay/dash/wiki/Whitepaper

**[7]** Boneh, D., Lynn, B. & Shacham, H. J Cryptology (2004) 17: 297. https://doi.org/10.1007/s00145-004-0314-9

**[8]** D. Boneh, M. Drijvers, and G. Neven. Cryptology ePrint Archive: Report 2018/483. https://eprint.iacr.org/2018/483.pdf 
