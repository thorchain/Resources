# Yggdrasil Protocol; Multi-Validator-Set Sharding


## A trust-minimized sharding protocol for multi-chain scalability on THORChain. 



devs@thorchain.org
V0.1.4 - July 2018

### Abstract 

>Current technology for public blockchains is limited in terms of horizontal scaling. Nodes generally maintain a full copy of the blockchain’s state and transaction history, limiting transaction throughput. 
>THORChain is highly-optimized blockchain with a focus on scalability. An efficient consensus algorithm based on practical Byzantine Fault Tolerance (pBFT) is combined with a multi-chain approach, in order to provide optimum transaction throughput. The THORChain multi-chain has n number of canonical chains with n discrete mem-pools, which are synced on a master chain.
>THORChain employs an entirely new byzantine resistant sharding mechanism for multi-chains with discrete mem-pools suitable for proof-of-stake based protocols. Each shard is randomly assigned and cross-validated by a group of Validator Sets, the Validating Group, requiring supermajority consensus for new blocks to be committed to canonical chains in the shard. The network can be sharded to an upper bound limited by a number of Validator Sets.  
>Shard consensus requires s validator sets to cross-validate in a validating group, with ⅔ s being byzantine resistant consensus. A validator set is composed of a number of validators. Validators are selected from a pool of online staked nodes. As a shard approaches saturation, it is split into several shards. Each validator set collects pending transactions from mem-pools they observe and proposes blocks to all canonical chains in their shards. 
>The protocol exhibits safety with two levels of Byzantine resistance; supermajority consensus in both the validator Sets and the validating Group but is vulnerable to plutocracy. This can be countered by minority-enabling economic incentives that prevent stake centralization and encourage a flat distribution of stake across all validators in each validator set. A solution to this is proposed in this paper and will be integrated into the economic model of THORChain to allow it to support multi-set sharding.




## Overview

Introduction

Overview

[Sharding](#sharding)
  - Blockchain Scalability
  - Sharding Background	
  - Challenges and Trade-offs
  
[THORChain Sharding](#thorchain-sharding)
  - Algorithm	
  - Cross-Shard Communication	
  - Performance Estimation	
  - Trade-offs	
  
[Conclusion](#conclusion)	



## Introduction

### Overview


Public blockchains traditionally scale very well in terms of the number of nodes supported. However, there generally are severe limitations in terms of transaction throughput scalability. [Ethereum]( https://www.ethereum.org/), for example, supports approximately 15 transactions per second. Considering that all transactions and their associated state changes have to be processed by all nodes, this limitation is not surprising. 
Poor blockchain scalability in terms of transaction throughput is probably one of the two most limiting factors holding back the development of large-scale decentralized applications. The second factor, transaction cost, is related to transaction throughput, as cost should go down once transactions are not competing for scarce bandwidth.
THORChain is a blockchain purposely built with scalability in mind and implements several measures to guarantee high transaction throughput. The main use case targeted by THORChain is the implementation of a decentralized cryptocurrency exchange.
The platform architecture is based on a multi-chain solution, allowing multiple sidechains to settle on a root chain, called MerkleChain.  MerkleChain stores the root hashes of the sides chains’ transactions [Merkle trees](https://en.wikipedia.org/wiki/Merkle_tree ). Each sidechain represents a token, with a separate mempool. However, as all token chains share the same global address space, inter-side-chain communication is trivial, and transactions can always be found.
Consensus in THORChain is achieved using a delegated Proof of Stake protocol with 100 validator nodes per validator set that reaches consensus using a [pBFT voting protocol](http://pmg.csail.mit.edu/papers/osdi99.pdf ) [1]. Validator nodes are chosen according to the number of coins staked. Built-in reward and penalty mechanisms guarantee correct validator behavior at the protocol level. Malicious actors can also be evicted and replaced by a node from a queue of backup validators.
The focus of this paper is on sharding. Traditionally this method of scaling has received little attention due to its complexity and trustful nature of cross-shard transfers. Currently, only Ethereum and Cosmos appear to be pursuing similar solutions. That said, sharding is the only base-layer scaling solution which reduces the requirements of individual validators. The trust-full nature of sharding is that in cross-chain transfers it is not possible to be sure that the crediting and debiting of funds is done appropriately without validating other shards. While it is always safe to send tokens to another shard (worst case scenario is they don't credit it), receiving tokens from another shard risks inflating the overall money supply if they have not properly debited the sent tokens on their shard. In order to completely minimize trust, each validator must be responsible for every shard. Of course, this removes the benefits of sharding entirely. 
We propose a solution that is a better balance between the fundamental trade-offs in sharding solutions. In what remains of this paper, we will provide some further background on blockchain scalability and sharding, before detailing THORChain’s approach. 


## Sharding 
### Blockchain Scalability
Scaling is perhaps the most pressing concern for current blockchain technologies. Scalability involves a number of trade-offs. Ethereum’s co-founder Vitalik Buterin describes the scalability trade-off as a [trilemma](https://github.com/ethereum/wiki/wiki/Sharding-FAQ) between scalability, security, and decentralization (Figure 1). This basically means, that in order to optimize a blockchain for scalability, either security or decentralization needs to be relaxed.

<img align="center" src="https://github.com/thorchain/Yggdrasil-Protocol/blob/master/docs/images/scalability-trilemma.png" width="400" height="215" />

_Figure 1 : Scalability Trilemma_ 

This can be seen in third-generation blockchains focusing on scalability, such as [EOS](https://eos.io/ ) reducing the number of validators to a mere 21, thereby introducing a risk of centralization. 
Scaling proposals can be classified into two categories:
Off-chain scaling consists in second-layer solutions that execute a number of transactions off the blockchain and use the blockchain's ledger for occasional settlement. By doing so, the requirement for sequential consistency [2] is relaxed. Bitcoin’s [Lightning](https://lightning.network/ ) and Ethereum’s [Raiden Network](https://raiden.network/ ) fall into this category.
On-chain scaling solutions may entail modifying the consensus protocol, such as THORChain’s own model, in which a number of validators are chosen from a pool of staking nodes to execute an efficient variant of the Practical Byzantine Fault Tolerance [3] protocol to greatly improve transaction throughput and achieve very low-latency block finality. Other on-chain approaches include tweaking certain blockchain parameters, for example, Bitcoin Cash’s block size increase. 
In between the two extremes are solutions that split up the blockchain into manageable pieces, in order to reduce the load on individual nodes. This split can be done vertically along application specific divides, as in the side-chain approach ([Lisk](https://lisk.io/), [Plasma](https://plasma.io/) [4]) or [THORChain’s multi-chain solution](). It can also be done horizontally, through sharding. 


### Sharding Background
Sharding is a technique common in distributed data management systems. In databases, tables are split horizontally across rows into different shards. Different shards are stored by different nodes to spread the load.  
Sharding proposals for blockchains are similar in nature. The basic idea is dividing either transaction processing, global state or both into shards maintained by different sets of validators. A set of root validators typically synchs between shards. This process is illustrated in Figure 2.

<img align="center" src="https://github.com/thorchain/Yggdrasil-Protocol/blob/master/docs/images/merkletrie.png" width="400" height="215" />

_Figure 2: Sharding Concept_

One of the first sharding proposals for modern blockchain was ELASTICO [5]. Similar approaches, such as early sharding proposals for Bitcoin based on [Merklix trees](https://www.deadalnix.me/2016/11/06/using-merklix-tree-to-shard-block-validation/ )  and [Zillqua](https://docs.zilliqa.com/whitepaper.pdf ) share the same property of only focusing on one of the two components: state or transactions. This means they either improve transaction throughput or a node’s storage requirements, but not both. 
[Ethereum’s sharding solution](https://github.com/ethereum/wiki/wiki/Sharding-FAQs ) is still in active research. However, the goal of Ethereum sharding is to shard state, as well as transaction processing. 
In a linked list of blocks is sequential. Splitting up a Blockchain into different shards requires a more hierarchical approach. A number of individual chains are created, one for each shard. In order to maintain the overall chain, these shards need to somehow connect to the main chain. 
Ethereum introduces a hierarchy of four node types for this purpose:
Super full-nodes maintain every collation and the main chain. They also integrate collations from different shards into main chain blocks.
Top-level nodes process the main chain and give access to all shards.
Single-shard-nodes are the same as top-level nodes, but also maintain all the collations of their particular shard.
Light nodes only maintain block headers from the main chain but can request state from different shards when required.
In a sharded system following this model, blocks are valid when the transactions in all included collations are valid. Additionally, all collations need to be signed by a certain percentage of collators, typically two thirds.

### Challenges and Trade-offs
#### Single-Shard Takeover Attack
A problem in sharded blockchains is that each shard is now maintained by a much smaller number of nodes than the whole chain. It is thus theoretically much easier for an attacker to get hold of a sufficient majority in a single shard to manipulate data.
This problem is known as the `1%` attack, based on the assumption that in a `100` shard system it takes [1% of the networks](https://cosmos.network/whitepaper#appendix ) hash rate to dominate the shard.
This problem can be mitigated by choosing validators for shards through random sampling and changing this sampling frequently. 
Choosing and changing collators randomly is much easier in proof of stake-based systems, as collator nodes can just be randomly sampled from the set of validators that participate in staking. 

#### Cross-Shard Communication
Communication between shards has to be performed via the main chain. The difficulty, in this case, is to maintain the atomicity property of transactions.
Let’s consider this issue focusing on digital assets, such as a token. Sharding must have a protocol for shards to interact and swap tokens. A simple protocol for two shards to send tokens amongst themselves can be thought of as a credit-debit system. The sending shard debits a cross-shard payment while the receiving shard credits the sending shard new tokens.
The fundamental problem with sharding is that with non-overlapping validator sets is for the receiving shard to be sure the sending shard properly froze the debited funds.  For such a system to work each shard must have some basis for trusting other sending shards to properly debit cross-shard transfers. While this is not trust-minimizing, with public blockchains it is easy to tell if there has been a violation provided that copies of the shards exist independent from the validators, who themselves could cheat.
It might see that this limitation could be circumvented if a single validator set was responsible for all shards. The issue here is that in such a case the benefits of sharding are voided. If one validator set is overlooking all shards, they might as well be validating a single chain instead, since the computational requirements are approximately the same.

## THORChain Sharding
### Algorithm
THORChain sharding solution may seem counter-intuitive at first. However, the system presented in this paper achieves two goals. Firstly, the requirements of validator sets are reduced to a constant number of shards. Secondly, a high level of trust-minimization is achieved for cross-shard transactions. For this reason, we describe our THORChain’s sharding solution as trust-minimized sharding. 
In THORChain, each shard is maintained by multiple validator sets and each validator set maintains several shards. 
The number of shards in the system is defined as `N`. Each validator set is responsible for exactly three distinct shards. Let there be one validator set for each distinct set of three shards. Thus, the number of validator sets `S` necessary to cover `N` shards can be calculated as `N choose 3`.
For cross-shard transfer to be confirmed two thirds of the validator sets covering them must agree.  For each validator set to be compensated, it can participate in validating individual shards and retain a block reward. The number of validator sets overlooking an arbitrary shard `s` is `(N - 1) choose 2`, which is equal to `(N-1)(N-2)/2` and grows approximately as the square of `N`. Figure 3 shows an example of four shards being covered by four validator sets `(4 choose 3 = 4)`.

<img align="center" src="https://github.com/thorchain/Yggdrasil-Protocol/blob/master/docs/images/sharding.png" width="400" height="215" />

_Figure 3: Multi-Sharding Example_

### Cross-Shard Communication
The purpose of multiple shard assignments in THORChain is to prevent fraudulent cross-shard communication. As explained above, transferring assets across shards is akin to atomic debiting and crediting in the involved shards. For simplicity, let's consider `S_a` and `S_b` two be two arbitrary shards. 
Consider the number of distinct validator sets responsible for these two shards across all shards `S`. As each validator set covers three shards, all validator sets involved cover shard `S_a`, `S_b` and some third shard, which is neither `S_a` nor `S_b`. Since we earlier said that we would have one validator set for each unique set of three shards, it must be the case that there is a validator set that covers `{S_a, S_b, S_k} for k = 0, ..., N where k != a and k != b`. It is clear here that `k` can take on `N-2` different values. That is, there are `N-2` distinct validator sets that cover any arbitrary two shards `S_a` and `S_b`. In the event any single validator set tried to produce a fraudulent transaction by faking a credit or debit, this action would be immediately noticed by the other `N-3` validator sets.
For same-shard transactions, each validator overlooking that respective shard participates `((N-1) choose 2)`. For each cross-shard transaction, each shard that also overlooks those two shards participates in the transfer process `(N-2)`.
For a cross-shard transfer to happen, a two-thirds majority of sending shards validators is needed for the transaction to be submitted. The shared validators between the two shards vote on whether to approve or deny the transfer. After the second vote, there is a waiting period, in which two thirds of the receiving shard’s validators can override an approval and burn the bonds of the shared validators. 
In either case, a two-third majority of validators of the receiving shard send a message to the sending shard confirming the transaction acceptance or denial. 
Note that the small number of shared validators across two arbitrary shards does not pose a large amount of risk. In the event two thirds of the shared validators were compromised, they cannot initiate transfers themselves. Compromised validators can only block transfers.  However, as there are `N-2` other shared validator sets through which a cross-shard transfer can arrive at the receiving shard, this does not amount to censorship.  
The biggest risk in our sharding approach is a compromise of two thirds of the validators overlooking a single shard. In this case, the compromised validators could mint money on their own chain by breaking consensus rules. However, since these blockchains are public, it would be impossible to hide this, and other shards can deny incoming transfers from the defective shard. Since all validators know the public keys of all other validators and which shards they maintain, it is possible for validators to vote to replace malicious validators.
One important property of the THORChain sharding approach is that as the number of shards grows, the number of validators required becomes relatively large. Because of this and the multi-shard overlap, the validator sets themselves can actually be quite small.  In fact, we can consider this algorithm as having validator sets with as few as one node each.

### Performance Estimation
THORChain requires a large number of validators, which are selected by bonding a minimum amount. The first cryptocurrency known to use masternodes was Dash [6]. Let’s use Dash to consider a preliminary performance estimation for the THORChain sharding approach. 
In Dash, there are currently `4000` validators. With this many bonded validators, the THORChain algorithm could maintain `30` shards, assuming one-node validator sets. Since each validator need only validate three shards, this means bandwidth, space, and computational requirements are reduced by `90%`.
For a cross-shard transfer to happen, a `2/3` majority would be needed from the sending shard and a transaction would be submitted to the receiving shard. Then the shared validators between the two shards would vote on whether to approve or deny the transfer. Afterward the second vote, there would be a waiting period where `2/3` of the validators on the receiving shard could vote to override an approval and burn the bonds of the shared validators. In either case, a `2/3` majority in the receiving shard would send a message to the sending shard the final status (accepted or declined). This `2/3` majority is not intended to due validation themselves but the intention is that enough time is given so that if anybody notices a discrepancy, the validators could be informed and look into this. Each of these voting mechanisms is meant to prevent the minting of tokens. Furthermore, the shared validators can earn a transaction fee by participating in cross-chain transfers incentivizing them to approve legitimate transfers.

### Trade-offs
The main trade-off with our sharding approach is that the number of validators grows cubically with the number of shards. As the number of shards grows, a much smaller number than two thirds of all validators need to be compromised to perform an attack. That said, any compromised shard can be isolated from other shards by denying incoming transfers. Furthermore, it is possible to eject malicious validators if a majority of the overall validators is obtained. Interestingly, as the number of shards grows the storage, bandwidth, and computational requirements remain constant for individual validators.

Our scheme provides a balance between decentralization, security, and trust-minimization, optimizing the above-mentioned scalability trilemma. Decentralization comes with constant storage, bandwidth, and computational requirements regardless, of the number of shards. As the number of shards grows, so must the number of validators. Security here is clearly less than that of a single blockchain but there are mitigation strategies if a shard becomes defective. 
Finally, this scheme far less trust-full than systems, such as EOS, as the number of validators contributing to consensus is much higher.

## Conclusion
We have presented the THORChain sharding solution, a novel multi-sharding solution suitable for high transaction throughout blockchains. Out approach optimizes the tradeoffs involved in sharding by covering each shard with multiple validator sets. Security of cross-shard transfers is increased through majority voting. 
We have shown how the number of validator sets increases with the total of shards in existence, whilst at the same time reducing the number of nodes required per validator set. 

## References

**[1]** Miguel Castro and Barbara Liskov. 1999. Practical Byzantine fault tolerance. In Proceedings of the third symposium on Operating systems design and implementation (OSDI '99). USENIX Association, Berkeley, CA, USA, 173-186. http://pmg.csail.mit.edu/papers/osdi99.pdf 

**[2]** Lamport, L. (1979). How to make a multiprocessor computer that correctly
executes multiprocess programs. IEEE Trans. Computers, 28(9):690- 691.

**[3]** Miguel Castro and Barbara Liskov. 1999. Practical Byzantine fault tolerance. In Proceedings of the third symposium on Operating systems design and implementation (OSDI '99). USENIX Association, Berkeley, CA, USA, 173-186

**[4]** Plasma: Scalable Autonomous Smart Contracts. Joseph Poon and Vitalik Buterin. 2017. https://plasma.io/plasma.pdf 

**[5]** Loi Luu, Viswesh Narayanan, Chaodong Zheng, Kunal Baweja, Seth Gilbert, and Prateek Saxena. 2016. A Secure Sharding Protocol For Open Blockchains. In Proceedings of the 2016 ACM SIGSAC Conference on Computer and Communications Security (CCS '16). ACM, New York, NY, USA, 17-30. DOI: https://doi.org/10.1145/2976749.2978389

**[6]** Evan Duffield, Daniel Diaz. Dash: A Privacy-Centric Crypto-Currency. https://github.com/dashpay/dash/wiki/Whitepaper 
