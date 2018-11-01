# Bifröst Protocol: cross-chain bridges

## Enabling performant cross-chain bridges with full protocol security on THORChain.

devs@thorchain.org
V0.2 October 2018

### Abstract
>We propose an effective chain-agnostic bridge protocol that uses multi-signature accounts, cryptoeconomics and continuous liquidity pools (CLPs) to ensure security of assets traded across bridges on THORChain. This can be adapted for almost all major UTXO, account and contract-based assets including Bitcoin, Ethereum, their code-forks as well as their tokens. Validators are staked fully synced nodes that are part of the Validator Set for THORChain and secure the protocol.  The Validator Set nominates a sub-set of Validators to form `k` bridges by being party to `m of n`, `n-1 of n` or `n of n` multi-signature accounts on the external chain. Each bridge offers different but observable security and performance characteristics to be selected by users to match their needs. The security of the bridge is `m * stake`, where stake is the stake held by each Validator in the sub-set. Incoming coins are locked in the multi-sig and minted as `tCoins` via CLPs on THORChain. Once minted, the `tCoins` are secured by the entire protocol on a single tokenChain and each tCoin exhibits verifiable fungibility despite being minted by different sub-sets on different bridges. THORChain tokens can also be safely deployed and recovered on any supported external chain via CLPs and the existing bridges. The CLP transmits a price for the `tCoin` which can be used by the protocol to measure the risk of each bridge; a risky bridge is one where the bridge security is less than the value of locked assets. The Validator Set will then re-shuffle a risky bridge, or move assets away to a more secure bridge; thus the bridges inherit the entire protocol’s security. If a bridge is attacked, the Validators will slash the attackers and use slashed assets to restore the stolen coins, via on-chain governance and intervention.

### Document Set
The following whitepapers should be read in conjunction:


- [THORChain](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md)
A lightning fast decentralised exchange protocol.

- [ASGARDEX](https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/whitepaper-en.md)
A lightning fast decentralised exchange built on THORChain.

- [Bifröst Protocol Networking Spec](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol/networking-whitepaper-en.md)
Secure bridges to THORChain without hard security guarantees

- [Flash Network](https://github.com/thorchain/Resources/tree/master/Whitepapers/Flash-Network/whitepaper-en.md)
A layer 2 Network for instant asset exchange on THORChain.

- [Yggdrasil Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Yggdrasil-Protocol/whitepaper-en.md)
Dynamic multi-set sharding for THORChain.

- [Æsir Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/AEsir-Protocol/whitepaper-en.md)
A self-amending forkless consensus algorithm for THORChain.

## Overview

[Introduction](#introduction)
- Cross-chain Interoperability
- Cross-chain Bridges
- Atomicity

[Existing Cross-chain Solutions](#existing-cross-chain-solutions)
- Rootstock 2WP
- Liquid Sidechain
- POA Network Bridge
- COSMOS Peg Zone
- Full Network Security

[Bifröst Protocol](#full-network-security)
- Overview
- Considerations
- Processing Bridge Transactions
- Randomised Reshuffling Sub-sets
- Supervised Sub-set Signatories
- Signature Thresholds
- On-chain Governance

[Implementation](#implementation)
- Entering a Bridge
- Trading tCoin
- Exiting a Bridge
- Bitcoin Bifröst
- Ethereum Bifröst
- Monero Bifröst
- Bifröst CLPs
- Generating a Bifröst CLP
- CLP Transactions

[Fungibility](#fungibility)
- Overview
- Avoiding Race Conditions
- Managing External Fees

[Security](#security)
- Overview
- External Lone-ranger Attacks
- Internal Lone-ranger Attacks
- Validator Attacks
- Network Attacks

[Networking Overview](#networking-overview)	

[Economic Incentives](#economic-incentives)
- Discouraging Malicious Behaviour
- Encouraging Running BridgeNodes
- Faking Blockheight or using Remote Nodes

[Quorum](#quorum)
- Building Quorum
- Dropping from Quorum
- Re-entering Quorum
- Permanently Lost Quorum
- Chain Reorg or Split

[Exit Fees](#exit-fees)
- Bridge-chain Transaction Fee
- Bridge-node Exit Fee
- Fee Market

[Publishing Fraud Proofs](#publishing-fraud-proofs)
- Unauthorised Spend
- Unauthorised Mint

[Conclusion](#conclusion)


## Introduction
THORChain is an entirely new protocol being built to serve the needs of a liquid, instant and diversified future of value transfer. As it exists solely to connect all cryptocurrency assets with each other, cross-chain compatibility is a cornerstone feature of the protocol. THORChain has native features that allow it to become the desired ecosystem for all digital assets, and extremely easy to move assets on (and off) the network. Indeed, assets moved on to THORChain may be more valuable than their original counterparts due to a more favourable trading environment, lower fees and staking revenue opportunities available in THORChain, but not available on external chains.

### Cross-chain Interoperability  
Since the original Bitcoin implementation, many blockchain solutions have emerged, all focusing on different characteristics. Some chains value security over performance, whereas others target high-throughput and low latency applications. Other blockchains may focus on privacy. Usually, favoring one feature over another involves trade-offs. Ethereum's lower block generation time, for instance, results in a larger amount of stale blocks, compared to Bitcoin. As a result a plethora of blockchains exist, each holding assets of some value.
Moving information across chains in a multi-blockchain world is thus a necessary and highly-desirable part of the ecosystem. Copying information from one centralized database to another is trivial with the right access; but transferring information across immutable blockchains is unavoidably difficult. Not in the least is that both blockchains need to agree on the shared truth; but it is desirable to do in a trustless manner to be aligned with the fundamentals of the ecosystem itself. Most likely the immutable information that needs to be moved is value; and with value comes very strong incentives to intercept, double-spend and redirect that value.
There are a number of solutions to this problem that have been proposed and work somewhat, but most have to make tradeoffs between trust, security and performance. It is desirable to have a solution which is highly trustless, byzantine fault tolerant, is useable and has observable security.

### Cross-chain Bridges
Cross-chain Bridges are simple; an asset is locked on one chain; whilst an identical asset is atomically “minted” on the other and sent to an address owned by the original party. The newly minted asset has fungibility with the original one by virtue of the fact that it can be used to redeem the original asset at the same ratio that it was minted. This newly minted asset represents the rights to unlock assets on the original chain; so as long as the bridge continues to exist without censorship or interference, then the tokenised asset can be handled as though it was the original asset. When the owner (anyone who has keys to spend) wishes to move back to the original chain, it is done via the same bridge; the asset is destroyed on the bridged chain and atomically unlocked on the original chain. Destruction can be done by provably burning the token (UTXO-based or account-based) or by deleting the token and decrementing the total supply of token if it is contract-based.

### Atomicity
A fundamental part of the cross-chain bridge mechanism is that it must be atomic; a failed transfer must not cause a double-spend opportunity on the bridged chain. Time to finality on the host chain complicates this; as the bridge must wait on a threshold of finality before it mints new tokens. Typically this involves waiting for confirmations, such as six confirmations for Bitcoin and 24 for Ethereum.
Finality is influenced by blockchain re-organisation, where based on block propagation latency and the possibility that nodes may discover a longer chain or a GHOST, `0-conf` transactions are not safe to work with. This drives exchanges and merchants to wait for confirmations that they feel safe with.
By virtue of what a blockchain is; finality is never 100% - there is always a possibility that the chain can be re-written with sufficient hash or staking power. There have been instances on smaller chains of re-writes despite as much as 50 confirmations with sufficient attack power. Typically the attacker will suffer an economic loss in attacking, but if the economic loss is less than the economic gain from a re-write then incentives favour the hacker.
A hard fork may also cause issues to the bridge in terms of what is considered to be the canonical chain. Tokens that have already been transferred to the bridged chain will have rights to spend both sets of coins in the forked chains, whether or not they have replay protection. Manual intervention may be required during times of hard fork events due to network uncertainty.

## Existing Cross-chain Solutions

### Rootstock 2WP
[Rootstock](https://uploads.strikinglycdn.com/files/ec5278f8-218c-407a-af3c-ab71a910246d/Drivechains_Sidechains_and_Hybrid_2-way_peg_Designs_R9.pdf) is an ethereum-compatible smart contract side-chain solution for Bitcoin. Rootstock have built a bridge that allows users to send Bitcoin to a multi-sig and have tokenised Bitcoin minted on the secondary chain. Rootstock use a concept of merge-mining and a drive-chain/side-chain implementation that gives primary custody to miners, with notaries in lieu of miner participation. It is preferred to have full miner participation in the consensus; however this is unlikely so a federation of notaries is employed to make up the security. Miner signalling is affected by the addition of an OP code that they insert in the coinbase. The presence of a federation is the primary drawback of this solution; parties must still trust a group of people to be honest.

### Liquid Sidechain
[Liquid Sidechain](https://blockstream.com/strong-federations.pdf) is another Bitcoin sidechain solution that uses the concept of a Strong Federation to secure the bridge; essentially notaries in the same sense as Rootstock. It is not clear who can be a signatory, or if there is a limit to the number of notaries. This still does not escape the scourge of trusting a party.

### POA Network Bridge
[POA Network](https://poa.network/) is a sidechain to Ethereum with faster consensus; a Proof-of-Authority consensus algorithm where renowned members of the community put up their public reputation and identity to be block producers. The cross-chain bridge in this case is quite trivial as a multi-signature account tied to block producers is sufficient. Although efficient, the notion of having individuals control the bridge (and consensus) is a trustless tradeoff.

### COSMOS Peg Zone
[COSMOS](https://blog.cosmos.network/the-internet-of-blockchains-how-cosmos-does-interoperability-starting-with-the-ethereum-peg-zone-8744d4d2bc3f) bridge the Ethereum chain with a peg zone that overlays a finality layer to Ethereum’s probabilistic finality and convert the signature schemes between the two chains to make them compatible. The witness that signs the peg zone is a `2 / 3` consensus from the COSMOS Validator Set; so the bridge security matches the security of the entire COSMOS chain. This implementation does not make many compromises between trustlessness and efficiency. The only issue here is that it involves the full participation in the nodes that secure the COSMOS network, so performance characteristics are unknown.   

### Full Network Security
All pegs attempt to involve full network security in their bridges, however most concede defeat to trusted or semi-trusted bridges with less than full network security. The exception is Cosmos, which has full and trustless network security. Any less than full network security on a bridge means that a cartel always has the economic incentives to conduct a supermajority sybil attack and seize assets if they are valuable enough.

## Bifröst Protocol
### Overview
The Bifröst Protocol is the solution for THORChain and combines full network security and oversight to cross-chain bridges with mechanisms to make bridges useable and performant. Additionally it requires no changes to the underlying blockchains that it forms bridges with. The bridges have compatibility with all major UTXO, Account and Contract-based cryptocurrencies.
The core Validator Set (100 staked Validators) are chosen to be signatories to multi-sig accounts to external chains. The external coin is moved into a multi-sig account, signed by the Validator Set. After observing finality, the Validator Set mints new tokens in THORChain via CLPs. The user can then exit via the same bridge. The token is destroyed via the CLP and then unlocked in the external multi-signature account to be sent to the user’s address.

In contrast to other blockchain bridging solutions, Bifröst uses a guilty-until proven guilty approach to validator security. Instead of assuming fraudulent intentions from the start, Bifröst focuses on providing configurable levels of security, which rely on the fact that cheating can be detected and proven, and also economically unviable for rogue validators.

### Considerations
There are a number of considerations that need to be addressed for this to work.

**Infrastructure.** The bridge requires all Validators to maintain fully synced nodes to all external chains. This involves a significant  infrastructure overhead. Some chains may become out of sync from a crashed daemon, or some Validators may not have all chains synced at all. To subsidise the cost of infrastructure Validators are paid from the block rewards of the protocol. Each Validator must gossip the last block height from each chain and becoming out of sync by `n` blocks (determined by the protocol) causes the Validator to be penalised by being slashed. At some point of being out of sync of any of the chains by more than the specified `n` blocks, the Validator is evicted from the Validator Set. This ensures that all Validators in the Set are in sync in external chains and can process external transactions.

**Performance.** If there are 100 Validators in the Set, and `67 / 100` multi-signatures are required on external chains, performance will be a significant issue. For UTXO coins (especially ECDSA-based signature schemes) the bridge will be expensive, taxiing on the external network resources and soak up block sizes. For contract-based coins like ERC-20, the `67 / 100` multi-signature transaction will take over 67 blocks to execute and delay finality. For Cryptonote coins such as Monero and Loki where m of n multi-signature isn’t supported, a `n-1 of n` or `n of n` multi-sig is required. Thus `99 / 100` or `100 / 100` will be next to impossible to orchestrate, even with full Validator Set participation. The solution is to reduce the size of the multi-sig required, but retain full Validator Set oversight, known as Supervised Subset Signatories (SSS) discussed in § 4.2.

**Redundant.** THORChain’s Validator Set is designed to be flat and highly competitive to enter, so it is likely that Validators will be recycled regularly. Additionally a Validator may drop an external chain and be evicted, or may lose access to a private key and have a multi-sig compromised, as well as the risk associated with maintaining a `n-1 of n` multi-sig, the protocol must be redundant in how it maintains multi-sig accounts. The solution to this is Randomised Reshuffling Subsets (RRS), discussed in § 4.1.

**Fungibility.** It is imperative that all  minted tokens have fungibility with original assets, as well as with each other. If a chain is bridged via multiple points then each token created from each bridge must be created from the same genesis on THORChain. The tokens must be so fungible, and the bridges must be so trustless, that tokens can enter via one bridge and exit via another with no loss in value. The solution to this is via use of THORChain’s in-built CLPs. CLP interaction is discussed at length in § 5.

**Adaptable.** The protocol must be able to tolerate external chain changes, such as hard forks, chain reorganization as well as adding new bridges to new chains at any time. Finally, the protocol needs to recover from the (unlikely) case of stolen assets from external accounts. The solution to this is effective on-chain governance with out-of-band community-sourced intervention. THORChain is built to be a self-amending ledger and the application of this that is relevant to the Bifröst Protocol is discussed in § 4.4.

### Processing Bridge Transactions
There are a number of innovations proposed to solve the salient issues of bridges. The Bifröst Protocol includes concepts such as Randomised Reshuffling Subsets (RRS), Supervised Subset Signatories (SSS) and Signature Thresholds. These innovations, coupled with diversified bridges, trustlessly observable risk, underwritten assets and on-chain governance, will mean that a mature implementation of the Bifröst Protocol constitutes the most effective way of transmitting value across chains to and from THORChain.

### Randomised Reshuffling Sub-sets
It is necessary to garner full protocol level security for bridges, but this comes at serious performance costs. The solution is ultimately a trade-off between security and performance as smaller signature requirements can be executed faster, but have higher risk. Using on-chain governance, `k` sub-sets of `n` signatures (such as `2` sub-sets with each `11` signatures) are voted to secure `k` Bridges. The Validator Set can use a Verifiable Random Function (VRF) to nominate individual Validators to be signatories to each of the `k` sub-sets and `k * n` Validators are required to be sourced from the 100 staked validators. Every `x` blocks the multi-sig accounts are re-shuffled creating new multi-sig accounts.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure1.png" width="400px" height="280px" />

*Figure: `k` subsets, secured by m of n signatures from n validators, party to `k` mult-sigs. Every `x` blocks, a validator is swapped out and assets moved.*

Although there will be `k` multi-sig accounts on the external chain, the Validators mint tokens on the same internal tokenChain. Importantly, only one Validator from each sub-set is removed and replaced by a random external Validator, as this reduces the risk of locking out a multi-sig.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure2.png" width="400px" height="250px" />

*Figure: Multiple bridges, one token. Sub-sets are re-shuffled regularly.*

The locked coins on the external chain will need to be moved as one transaction, signed by the parties required (`m of n`, `n-1 of n`, `n of n`, depending). The bridges can alternate between re-shuffling and signal ahead of time exactly when the re-shuffle will occur. After a bridge is re-shuffled it is a new multi-sig account; a consideration for users. If users send coins to a decommissioned bridge after it is re-shuffled (or during), there is a risk that not all Validators will be online or operating to honour the coin. This is especially true for `n of n` or `n-1 of n` multi-sigs, less so for `m of n`. The solution for this is building the correct user experience that communicates risks to users and showing the latest addresses. For contract-based coins there is the added benefit of using a proxy contract that will forward coins to the updated bridge. The Validators simply update the proxy contract at the same time as moving the coins. This proxy contract can be added in by on-chain governance.

**Diversification.**  Multiple bridges can be set up deliberately with different security and performance characteristics for the end user’s choice. A `3 / 4` Bridge is fast with low security, a `15 / 21` Bridge is slower but has more security, while a `20 / 21` Bridge would be the slowest, but with the highest security. As each bridge mints the same final `tCoin` which is secured by the entire protocol, users are simply making choices on the risk/performance of bridge transaction, as opposed to the security of the final `tCoin`. Entering on one bridge and leaving on another is also entirely feasible. This results in verifiable fungibility for each `tCoin`.

**Reliability.**  By re-shuffling validators and moving assets every `x` blocks in multi-sigs the protocol and users can be assured that Validators are online and have access to their private keys. This will reduce the risk of loss of asset if more than one validator goes offline. Additionally, by moving assets regularly, `n-1 of n` or even `n of n` can be attempted depending on security required. Further if a Validator in a sub-set is evicted for other reasons, or confesses to a compromised or lost private key they can be removed in schedule by signalling.

### Supervised Sub-set Signatories
It is necessary to supervise each sub-set with full protocol oversight to ensure that sub-sets are not victims to supermajority sybil attacks, where an attacker gains control of an entire sub-set to spend locked assets. The solution is for the Validator Set to observe each sub-set and take action when risks are higher than tolerated for bridges.

**Observing Risk**. The security of the bridge is thus equal to the sum of the stake for `m` validators in a `m of n` bridge if validators are completely slashed if they spend the locked assets. Thus rogue validators will observe the sum of locked assets and may consider an attack if they have more to gain by spending locked assets and being completely slashed. This is easy to observe and communicate to users. A bridge is risky when:

```m * stake <= sum(locked assets)```

By using trustless on-chain price feeds from CLPs the protocol can become self-aware of the risks of bridges. If the Validator Set observes the value of locked assets approaching the security of a bridge, they can do two things:

1. Spend a partial or full amount of the locked assets to a bridge with higher security, such as from the `3 / 4` bridge to the `15 / 21` bridge.  
2. Re-shuffle the bridge to a multi-sig with a higher `m`, such as from a `20 / 21` to a `21 / 22` bridge.

The mechanism for this can be built into the consensus rules of the Validator Set, making the entire process trustless. This mechanism guards bridges that have partial security with full protocol security.

**Optimising Performance.** In the same way that multi-sig requirements for multi-sig accounts can be increased if the protocol observes that the security of a bridge is close to the value of locked assets, they can also be reduced. In the example that $1m of `BTC` is held in a `20 / 21` bridge, but the value of the stake is $20m, then the multi-sig requirements can be reduced to `10 / 11`, where $10m is held. This will increase the performance of the bridge, but still have a factor of security over the value of assets. If the value of locked assets increases then in turn the signature requirements can increase.

### Signature Thresholds
BLS signature thresholds can be used to further prevent issues during Validator Set re-organizations, as only a threshold of signatures is required to be achieved, instead of a specific aggregation of signatures. For example, if `15 / 21` signatures are required for a particular bridge, it does not need to specify the  set of signatures required to form a `15 / 21` signature threshold, only that `67%` is required. The reverse occurs to move the coin off the ecosystem. This translates to flexibility in who can be part of a sub-set, and tolerates validators leaving and re-entering the Validator Set, which could be a frequent occurrence in a healthy and competitive environment of validators. BLS signature threshold theory has been extensively researched by [DFinity](https://dfinity.org).


### On-chain Governance

On-chain governance, (known as the Æsir Protocol) is deeply integrated into THORChain and is a cornerstone of the protocol. Validator Signalling is a perpetual and continuous process by which Validators signal support for on-chain proposals and integrate them. This process is outlined in detail in the Validator Signalling whitepaper. With effective on-chain governance the protocol can quickly adapt to a changing environment. This is especially required for managing cross-chain compatibility and its inherent risks.

**Underwriting Assets.** Locked assets can be underwritten with the value of the stake of the Validators that secure the bridge, allowing a recovery (full or partial) of stolen assets. If a bridge suffers a supermajority attack, the rogue validators will be slashed immediately by the Validator Set. The seized stake can then be used to liquidate on markets to restore the stolen assets. Firstly Validators must signal to freeze the slashed assets, then they need to be moved to a community or Foundation wallet. If community approval is gained the Foundation or Trustee can purchase assets to replace stolen assets to restore full fungibility.

**Chain Support.** All aspects of supporting external chains such as their selection and support, hard forks, finality thresholds, multi-sig addresses, bridge number and thresholds as well as proxy and token creation contracts can be administered using on-chain governance.


## Implementation

### Entering a Bridge

When a Bridge is first established using Validator Signaling, the `tCoin` and tokenChain is created by the Validator Set using a Genesis transaction `GenTX` with 0 supply. Each time a new coin enters via the bridge, the supply of the GenAcc in the CLP is updated accordingly by  the Validator Set, and the `tCoin` is sent to the recipient. The `GenAcc` is filled with liquidity from either the project team, Foundation or self-interested liquidity stakers. `GenTX`, CLPs and Liquidity mechanisms are explained in the [THORChain whitepaper](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md).

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure3.png" width="400px" height="343px" />

*Figure: Sending coins to THORChain.*

The tokenIndex is chosen as simply the next index available and the information is stored in the token `GenAcc`. In the case that Bitcoin is tokenised onto the T1 chain and Ethereum is tokenised onto T2, the GenAcc would store (simplified):

```{T1:([BTC multi-sig addr1, expiry], [BTC multi-sig addr2, expiry]), etc}```

This allows the Validator Set to update the external multi-signatures if they ever need to change, such as if they were compromised. The user experience is simple and trustless, with an overview similar to the following:

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure4.png" width="400px" height="250px" />

*Figure: Enter Bridge User Interface, queried from the GenAcc.*

Importantly the data can be verified at any time by reading the `GenAcc`, so the bridge interface can be displayed on any front-end. As infrastructure is subsidised by block rewards on THORChain the bridges can operate feelessly.
Contract-based cryptocurrencies such as ERC-20, NEP-5 and QRC-20 can also be sent on to THORChain using the same parent multi-signature address. The GenACC stores the token’s root contract address alongside the sequential tokenIndex. In this way, anyone can create TokenChains for contract-based cryptocurrencies and achieve deconfliction. The following would be the contract-based implementation:

```
{T7:(Parent tokenChain (T2), contractAddr);
T8:(Parent tokenChain (T2), contractAddr);
T9: etc}
```

This allows anyone to send any token via its parent bridge to THORChain. If already created, it is simply added to the existing chain. If it hasn’t been created a new tokenchain is added.

### Trading tCoin
Once inside the ecosystem tCoins can be transacted freely and fungibly. These include all trades and transactions types. If traders can observe that their `tCoins` have `1:1` asset backing and that original assets can be claimed at any time, then tCoins will be `1:1` to Coins. A running total of locked assets as well as minted assets would be publicly available and verifiable, allowing anyone to see full backing.  If traders can observe the security of a bridge in economic value and know that at any stage a loss of locked assets would be under-written by slashed validator stakes, then they are further reassured of full fungibility of assets.
Since bridges do not have to charge bridge fees, and transaction fees on THORChain will be substantially lower than the original chains, the tCoins *may* have a higher perceived value than original assets. Take for example December 2017, where Bitcoin fees were greater than $50 for a sustained period of time. Transacting on Bitcoin would erode value, whilst transacting a `tBitcoin` on THORChain would save a considerable amount in fees. Further, assets on THORChain can be staked in CLPs to earn on liquidity fees, with no inflation risk.
These characteristics are important to ensure that `tCoins >= Coins`. If this is achieved then CLPs will attract self-interested arbitrageurs to correct market slips and maintain `tCoin >= Coin` prices. THORChain can also offer a more attractive trading environment by using Foundation assets to purchase Bitcoin off the market, establish bridges and lock `tBitcoin` in the CLPs. This will effectively bootstrap the on-chain liquidity for the `tCoin`. Once bootstrapped self-interested stakers can add further liquidity to earn on liquidity fees, growing on-chain liquidity. These mechanisms are described in the [THORChain Whitepaper]((https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md)).  

### Exiting a Bridge
`tCoins` can be exited at any time by simply using the Bridge in reverse. The `tCoin` is sent to the CLP in a special transaction which destroys it and updates total supply by the Validator Sub-set. Once block finality is achieved ~1 second, then the Validators can sign the external multi-sig to release the assets to the user’s address. Signing the external multi-sig and the finality of the transaction is limited by external chain characteristics.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure5.png" width="400px" height="365px" />

*Figure: Sending coins out of THORChain.*

As with entering the bridge, users can choose from various bridges with different security and performance characteristics. On-chain or client side checks can ensure that a user does not exit a bridge with assets that will exceed the security of a bridge. Again, the value of the assets can be known on-chain by the CLP pricing.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure6.png" width="400px" height="260px" />

*Figure: Exit Bridge User Interface, queried from the GenACC.*

Token exits can also be performed, even tokens that have never been created on external chains. This can be done by allowing the Validator Set to be party to a token creating account, that simply deploys a token standard with variable supply. The owner of the contract is the multi-sig for the bridge. Each time a new token is sent, the multi-sig updates the supply in the token contract and forwards the tokens to the user address.
Users must specify which external chain they want to exit on, as it is their choice. As an extension of this, since the “original” asset is the THORChain asset which has a supply controlled by THORChain Validator Set, tokens can be exited onto multiple external chains, and still retain fungibility. A Rune could have `1:1` representative coin on Ethereum, as well as EOS and NEO. The THORChain MerkleChain DHT tracks assets and their linked chains.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure7.png" width="400px" height="400px" />

*Figure: A THORChain token is exited and created on a parent chain.*

Having cross-chain compatibility is imperative for THORChain to augment the entire ecosystem by bringing the benefits of decentralised trading and liquidity to all existing tokens. With the correct design, THORChain may be a far more favourable environment for digital assets to exist and be the ecosystem of choice for the creation of digital assets.

### Bitcoin Bifröst

THORChain validators host Bitcoin full nodes and publish their Bitcoin public keys with HD derivation. Any user can then elect to create a bridge to THORChain by following the steps:
1) Collect public addresses from `n` validators of their choosing
2) Create a `m of n` multi-signature account
3) Publish all bridge details on THORChain such as the address, confirmations required and nominated refresh cycle in blocks, and linked coin (Bitcoin).

THORChain validators will then start cycling through randomly chosen validators into the `m of n` multi-signature every cycle, and spending all held UTXOs to new multi-sigs:
1) Randomly assign another validator to be a party, by creating a `m of n` multi-sig with the new validator replacing a random old validator
2) Spending all UTXOs to the new multi-sig
3) Publishing new public keys

The user follows the steps below to enter the Bitcoin Bifröst:
1) Generates a THORChain destination address `tdAddr`
2) Creates a Bitcoin transaction that includes the `tdAddr` encoded in the 80 byte `OP_RETURN` output script to the multi-sig.

The validators then propose to mint the `tBTC` on THORChain using the following process:
1) Observe the incoming transaction to a Bifröst multi-sig with `x UTXOs` and transaction id `txid`, with the minimum required confirmations for that bridge.
2) Publish a proposal to mint `x tBTC` from the Bitcoin CLP to `tdAddr` and including the `txid` as proof
3) If 67% of validators also see the transaction confirmed with minimum requirements, the minting is committed and the transaction made.

Exiting is simpler:

1) The user publishes a transaction on THORChain to spend `x tBTC` to a Bitcoin destination address `bdAddr` via chosen bridge
2) The validators commit the transaction to THORChain at block `k`
3) At block `k+1` the validators sign to spend `x UTXOs` from the nominated Bitcoin multi-sig, publishing the `txid` as proof that it was authorised on-chain
4) If `m of n` validators sign the UTXO then the Bitcoin is released to the user's `bdAddr`

Notes:
1) All Bitcoin-based blockchains can be supported with this process, provided they have the 40 or 80 byte `OP_RETURN` feature or similiar
2) `OP_RETURN` is prunable from the Bitcoin blockchain so it will not cause Bitcoin blockchain bloat, as it is not necessary once the transaction has been committed on the THORChain side.
3) If it is not possible to demarc on-chain the THORChain destination address for THORChain, an alternative method of linking a single-use only multi-sig with a THORChain address is possible, described below.

#### Linking without OP_RETURN
An alternative method to linking a UTXO-based blockchain transaction to a THORChain destination account involves the following steps:

1) User creates a THORChain address and pre-funds it with Rune.
2) User creates a single-use only `m of n` bridge on the linking blockchain.
3) User then links the multi-signature address to their THORChain address on THORChain. This is an on-chain transaction that will require Rune to pay as gas.
4) Any incoming transactions to the multi-signature will created minted coins on the user's address. This link can never be broken and is not suitable to be used as a public bridge.

Useful links:
[Bitcoin Multi-sigs](https://en.bitcoin.it/wiki/Multisignature)

[Bitcoin RPC](https://en.bitcoin.it/wiki/API_reference_(JSON-RPC))

[Bitcoin Raw Transactions](https://en.bitcoin.it/wiki/Raw_Transactions)

[Multi-Sig Example](https://gist.github.com/gavinandresen/3966071)

[OP_Return](https://en.bitcoin.it/wiki/OP_RETURN)

[OP_RETURN Example](https://www.blockchain.com/btc/tx/6dfb16dd580698242bcfd8e433d557ed8c642272a368894de27292a8844a4e75?show_adv=true)

[OP_RETURN Questions](https://bitcoin.stackexchange.com/questions/31972/how-to-add-additional-information-to-transaction)

### Ethereum Bifröst

THORChain validators host Ethereum full nodes and publish their Ethereum public keys with HD derivation. Any user can then elect to create a bridge to THORChain by following the steps:
1) Collect public addresses from `n` validators of their choosing
2) Create a `m of n` multi-signature account using a smart contract template
3) Publish all bridge details on THORChain such as the address, confirmations required, nominated refresh cycle in blocks, and linked coin (Ethereum).
4) Publish the smart contract methods that validators will need to call on the smart contract to spend and add/remove parties to the signature

THORChain validators will then start cycling through randomly chosen validators into the `m of n` multi-signature every cycle:
1) Randomly assign another validator to be a party, by creating a `m of n` multi-sig with the new validator replacing a random old validator
2) Publishing new public keys

The user follows the steps below to enter the Ethereum Bifröst:
1) Generates a THORChain destination address `tdAddr`
2) Creates an Ethereum transaction that includes the `tdAddr` encoded in the extra data field and spends to the Ethereum multi-sig

The validators then propose to mint the `tETH` on THORChain using the following process:
1) Observe the incoming transaction to a Bifröst multi-sig with `x ETH` and transaction id `txid`, with the minimum required confirmations for that bridge.
2) Publish a proposal to mint `x tETH` from the Ether CLP to `tdAddr` and including the `txid` as proof
3) If 67% of validators also see the transaction confirmed with minimum requirements, the minting is committed and the transaction made.

Exiting is simpler:

1) The user publishes a transaction on THORChain to spend `x tETH` to an Ethereum destination address `edAddr` via chosen bridge
2) The validators commit the transaction to THORChain at block `k`
3) At block `k+1` the validators sign to spend `x ETH` from the nominated Ethereum multi-sig, publishing the `txid` as proof that it was authorised on-chain
4) If `m of n` validators sign the transaction then the Ether is released to the user's `edAddr`

Notes:
1) All Ethereum-based blockchains can be supported with this process, provided they have the extra-data feature or similiar
2) ERC-20 and ERC-223 tokens can be supported by whitelisting them in the Ethereum Bifröst and linking them to the correct coin on THORChain.
3) Similiar to Bitcoin, users can also elect to create a private-linked bridge to their own Rune Address and can make transactions with no extra-data input required. All Ethereum and tokens they whitelist observed on their private bridge will be minted into their account.

[Ethereum Gnosis Multi-sig](https://github.com/gnosis/MultiSigWallet)

#### Future Ethereum Bifröst additions
In addition to the basic protocol specified, there are two suggested augmentations that can be used to improve the Ethereum Bifröst. These augmentations will be relevant to other Bifrösts too, but will be initially explored for Ethereum.

##### SPV-like proofs (adapted from ideas in [4])
The basic Bifröst uses its own consensus mechanism whereby it just waits for `m of n` validators to sign transactions that allow ETH/ERC20 token transfers in either direction. This consensus mechanism effectively happens at a layer above the core THORChain and core Ethereum consensus.

Using block headers and merkle trees, we can create short proofs (similar to Bitcoin SPV/Light Client proofs) that allow us to depend only on the core underlying consensus without needing an additional layer above.

For the Ethereum -> THORChain direction, instead of minting based on the `m of n` consensus, we allow any relayer to submit a transaction to THORChain that consists of a proof that the corresponding transaction was included in a block on Ethereum. We would implement a basic light client within the core THORChain consensus that can verify this proof and then trigger the minting immediately. With this, even in the event of a consensus attack on THORChain, attackers would not be able to mint fake ETH or ERC20 onto THORChain without breaking protocol, so we have much stronger security here.

For the THORChain -> Ethereum direction, we need to create proofs that an Ethereum smart contract can verify. Tendermint's default consensus and serialization cannot be cheaply verified on Ethereum, but with a change to using Secp256k1 signatures for the THORChain consensus, an Ethereum smart contract could verify signatures onchain. With this addition, we do not theoretically increase security, as signatures would still come from the same THORChain consensus validators, but in practice we then gain the full THORChain blockchain security for our Bifröst, and so only be vulnerable to a full THORChain consensus attack. This solution would also remove the need for the additional Bifröst layer consensus, which means a simpler protocol with less code, a smaller attack surface and a more performant and cleaner codebase.

##### Gossip-based signature gathering
In order to run the system, the basic Bifröst requires, in both directions, for validators to post their signatures onchain to THORChain or Ethereum. This is inefficient, as we do not require consensus for each signature to be posted to the blockchain individually, we only require consensus on the full set of signatures to be posted. This means that, as an optimization, we could allow validators to communicate through the THORChain gossip network (or any offchain communication channel they choose) to share their signatures, and once the full `m of n` signatures are received by any validator, that validator can post all the signatures together in a single on chain transaction. This would mean only 1 transaction needed to go onchain (for either the Ethereum or THORChain direction), improving performance and reducing gas fees. With this augmentation we may still want to leave the original process intact, with each signature going onchain, as it may provide some additional protection as a backup system for the Bifröst in the event of complex network-level attacks against the offchain communication channel used.

### Monero Bifröst

The Monero Bifröst will have the following adaptions:

1) Use of [Payment ID](https://getmonero.org/resources/moneropedia/paymentid.html) in place of `OP_RETURN`
2) Limit to `n-1 of n` and `n of n` [multi-sigs](https://monerodocs.org/multisignature/)
3) Validators to sync key-images
4) [Private View Keys](https://monerodocs.org/cryptography/asymmetric/private-key/#private-view-key) to be published by validators to allow auditing of bridge funds

This set-up will also support all cryptonote-based blockchains, (such as [Loki](loki.network)).

### Security

As explained at length in the Security section, THORChain will require protocol level slashing rules to immediately slash a validator's stake if they attempt to spend from any of the multi-signatures without posting a valid `txid` first, or they spend to an incorrect user address with a valid transaction. Additionally, the multi-signatures must be re-cycled over a nominated period of blocks to ensure the bridges are secure and useable at all times. Lastly, the protocol must include rules to compare the value in each multi-sig with the security of the bridge (`m * stake` for a `m of n` multi-sig), and create automatic spend if the security threshold of the bridge reaches a pre-determined level, such as 50-90%. If there are no higher-level bridges, then the protocol must also have a feature to increase the signature requirements on an insecure bridge, ie from `m of n` to `m+1 of n` or `m+1 of n+1`.

## Bifröst CLPs

### Generating a Bifröst CLP

Continuous Liquidity Pools (CLPs) are a cornerstone of THORChain, generating on-chain liquidity, transmitting prices to the protocol, providing price anchors to the Flash Network, storing auditable token information and allowing fees to be paid in any token. The CLPs in the context of the Bifröst Protocol are used to mint, destroy, lock and unlock tCoins.
The Bifröst CLPs are a variant of variable supply tokens, explained in the THORChain whitepaper. The GenAcc stores the following information for a Bifröst CLP:

Token|T1|TokenIndex
|:---|:---|:---|
Ticker|BTC|Ticker to display
Name|Bitcoin|Name to display
Supply|100|Total Circulating
Decimals|8|Decimals
Reserve|1.0|Fractional reserve of the CLP.
Owner|SELF|The Owner is the protocol, and can only be changed by the Validator Set.
Bridges|`{[multi-sig addr1, expiry, minSig, numSig]; etc}`|Bridge Addresses with expiry times to re-shuffle. and m of n signature thresholds. Multiple bridges can be set.

*Table: A Bifröst Token CLP; here for Bitcoin.*


### CLP Transactions
There are a number of CLP transactions relevant to the Bifröst protocol, more than the basic transactions covered in the THORChain Whitepaper. The following is an overview.

**Creating.** Using Validator Signalling a new bridge is nominated, synced and set up with the correct information. An altruistic party (the Foundation) or a self-interested forward-thinking party (Project Team), will then bootstrap the chain by sending in the first liquidity deposit to create the CLP liquidity. There will be two liquidity deposits required, the Rune, then the tCoin.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure8.png" width="400px" height="260px" />

*Table: GenAcc for T1*

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure9.png" width="400px" height="330px" />

*Figure: Bootstrapping a new tokenChain*

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure15.png" width="400px" height="290px" />

*Figure: Creating liquidity in the CLP.*

**Minting.** Every time a new coin enters the bridge the Validator Sub-set then performs the following transaction on the linked CLP:

```
mint(balance, address)
```

The CLP will then:
```
- Create an additional amount of coins
- Immediately send them to the address
- Update the total supply in the tokenData field
```



<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure10.png" width="400px" height="385px" />

*Figure: Minting new tokens via CLP.*

**Destroying.** Every time a coin exits the bridge the Validator Sub-set then performs the following transaction on the linked CLP:
```
destroy(balance, address)
```

The CLP will then:
```
- Destroy the amount of coins from user address
- Update the total supply in the tokenData field
- Unlock tokens from the external multi-sig
- Send to user address
```

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure11.png" width="400px" height="280px" />

*Figure: Destroying tokens via CLP.*

**External Deployment.** A user can deploy their THORChain tokens to an external chain:

```
deploy(balance, address, parent)
```

The Validator Set will then:
```
- Destroy the amount of coins from user address.
- Deploy an external token contract with the correct balance on the parent Chain.
- Send to user address.
```

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure12.png" width="400px" height="240px" />

*Figure: Deploying new tokens via CLP.*

**External Recovery.** A user can recover their THORChain tokens from an external chain:

```
recover(balance, address, parent)
```

The Validator will then:
```
- Destroy the amount of coins from external wallet.
- Update the token contract.
- Unlock tokens from the CLP to the user’s address.
```
<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure13.png" width="400px" height="215px" />

*Figure: Recovering tokens via CLP.*

## Fungibility

### Overview
Original funds are always held in external multi-sig wallets, but are exposed to fees every cycle. These fees will slowly deplete the funds and cause a fungibility problem where the value of the assets represented on THORChain is less valuable than the assets held in esternal reserve wallets.

These fees need to be covered in a way that does not reduce fungibility or sets up an unfavourable race condition.

This section proposes a solution that:
- Enforces fungibility
- Avoides an exit race condition
- Discourages small exits

### Avoiding Exit Race Conditions
If a deficit is tracked, where `deficit = cycleFees * nCycles`, or more safely: `deficit = supplyCoin - supplytCoin`, then this deficit needs to be covered by some user of the system.

Fees should not be charged on incoming assets, as this will penalise bringing in liquidity to the ecosystem. Instead, an exit fee should be charged, but it shouldn't be pro-rata, as it doesn't solve the fungibility problem. In fact, it deterministically enforces it, ie, "due to fees, everyone's tBitcoin is now 0.98 Bitcoin".

The fees should not be a "last one out" fee either, as that will set up a race condition to exit, and not be last out.

### Managing External Fees

The fee should be a "first out" fee, where the first to exit is charged 100% of the deficit, no matter how large.

This has the following characteristics:
- this will discourage small exits, which add resource overheads to validators
- this discourages an exit race condition
- this enforces fungibility

## Security
### Overview
The Bifröst Protocol is designed with specific regard to attack vectors, and makes effort to mitigate risk with protocol security, cryptoeconomics and game theoretical approaches. Attack vectors can come from external chains, inside THORChain, inside the sub-sets and the most significant; a supermajority sybil attack on the protocol itself.
In this analysis the specific example to highlight and frame the discussion is when $100m in external assets are locked in multi-sigs, and the protocol’s Validator Sets have less than $150m in Rune staked; such that `67% = $100m`.

### External Lone-ranger Attacks

**External Assets.** Any assets locked in an external multi-sig account become a honeypot for attackers and incentivise attacks, let alone anonymous multi-sigs which is the proposition of the Bifröst Protocol. The private keys to secure the multi-sigs are held by specialised validators that hold stake in the THORChain ecosystem, not the external chain. Thus for an external attacker to double-spend or attack a multi-sig; the security threshold is unequivocally the same as the security of the entire chain linked. Chains with small hash power have historically been attacked, and indeed the attack cost is known ahead of time with services such as [Crypto51](https://www.crypto51.app); a tool to calculate the cost of `51%` attacks on various chains. In this case an attacker would attempt to gain `51%` network power for as long as they could re-write the chain for more than the number of confirmations that exchanges permit in the disposal of assets; typically 6 confirmations. This is around an hour of control for Bitcoin. By requiring internal stake, external attack vectors are subservient to the characteristics of external chains and this is a sufficient mitigation of risk.

**Internal Assets.** Assets held in THORChain are only vulnerable to internal attacks as the permissioned validators that control internal assets are always internal to THORChain. There is no attack vector here.

### Internal Lone-ranger Attacks

**External Assets.** Any assets locked in an external multi-sig account are controlled by permissioned validators. An agent would need to become permissioned before they could attack.

**Internal Assets.** Assets locked in internal accounts, such as CLPs, are secured by the protocol. An attacker would need to exploit a vulnerability in the protocol to siphon assets from CLPs and this would be patently obvious once the first attack occurred. An attacker would need to become permissioned as a validator to exploit other attack vectors.

### Validator Attacks
As part of THORChain’s design all Validators hold stake in the network, the amount set by the lowest bidder. An annual proposed inflation of 5% motivates holders to become Validators, or delegate tokens to Validators. Validators are incentivised to watch each other for rogue activity in order to slash an attacker’s stake and earn it for themselves. Thus an attacking validator must always consider full loss of stake as the economic cost for an attack.

**Single Validator.** Assuming the protocol works as designed with byzantine resistance, a single rogue validator is effectively risking their entire stake for an attack that will never be effective. They may attempt a double spend, but it is assumed a single honest validator (from a pool of 100) would notice and publish the proof of attack to slash the attacker. With correct protocol design the attack vector here is limited to the validator making an attack and getting away with it for as long as it not noticed. This can be mitigated by enforcing an unbonding period before a validator can reclaim their stake from resigning or being evicted as a validator. THORChain’s proposal of 14 days unbonding period is a sufficient period of time to allow retrospective auditing of blocks by the community and validators and impose slashing rules.  

**Validator Sub-set.** Validator sub-sets are nominated to control the keys to external chains, as well as aggregating signatures for signature thresholds for internal signing of CLP transactions. The value proposition of the Bifröst Protocol is that offers bridges with performance, whilst still retaining the security of the entire protocol. A key mechanism is that the protocol can observe the risk of a bridge by using prices from CLPs, and take action to reduce risk, or increase security. The inherent risks are a supermajority sybil attack in a sub-set. The protocol attempts to mitigate this risk by randomly appointing who can be part of a sub-set, as well as recycling validators every `x` blocks. The following are considerations for an attacker who can achieve supermajority in a subset, but not supermajority of the protocol:

1. A cartel need to first become permissioned into the Validator Set, which involves buying into the network and becoming fully-synced and compliant full nodes. Block rewards whilst being compliant would compensate the infrastructure cost.  
2. The cartel would need to then manipulate the CLP pricing of the chosen asset down in order to prevent the protocol from taking corrective action. Depending on the volume of the asset, this would incur a high arbitrage cost to them. The cartel would need to perform this for at least `x` blocks.
3. The cartel require the value of the locked asset (whilst they manipulate it) to increase in value to be higher than their combined stake; as their stake will ultimately be staked. Since they are manipulating the price of the asset down in (2), it would incentive traders to buy the cheap asset and send it off the network to sell at higher prices elsewhere. This would actually reduce the amount of locked asset in the external accounts, removing the honeypot.
4. If (3) does not work in time, the cartel would then need to rely on probability to be nominated such that they control a supermajority in any sub-set.
5. A cartel, who by chance, gains the supermajority in a subset has a limited amount of time to attack, x blocks, before they are likely to lose the edge.  
6. Assuming (4), the cartel would then need to spend the external assets prior to (5) and hide the act for the duration of the the unbonding period.

If their act is discovered inside the unbonding period, then they are slashed. This is highly likely. Their economic gain is thus:

```
(stolenAssets) - ((slashedRune) + (arbitrageCost * x blocks))
```

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/figure14.png" width="400px" height="350px" />

*Figure: The attack path to steal external assets*

The mechanics of manipulating CLP pricing in order reduce the perceived cost of an asset in order to allow an attack to occur is covered in the [THORChain Whitepaper](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md#trustless-on-chain-price-feeds).

### Network Attacks

**Supermajority Attacks.** The largest risk to THORChain is a protocol level supermajority sybil attack with an attacker gaining `67%` control of all validators. At `67%` control everything can become compromised, including external assets, internal assets, protocol rules and the validators themselves.
Arguably the cost of attaining `67%` of the Validators would be high and likely to cause a runaway price action on the Rune as `67%` of all bonded Runes would be bought by a single attacker. An attacker may instead try to attain the support of `67%` of the nodes to form a cartel, but again this is unlikely since the nodes are self-interested and have an ongoing infrastructure cost that needs to be compensated. Attacked THORChain would likely result in a devaluation of the network.

**Protocol Attacks.** An attacker may instead try to create an asymmetric attack vector by bringing in changes to the protocol via on-chain governance and Validator Signalling. The counter to this is creating a well-designed governance system with empowered minority mechanisms who can meaningfully influence voting; as minorities may have more awareness of long-range protocol attacks through crowd-intelligence. Validator Signalling is discussed at length in the [Æsir Protocol Whitepaper](https://github.com/thorchain/Resources/blob/master/Whitepapers/AEsir-Protocol/whitepaper-en.md).

## Networking Overview

The Bifröst Protocol builds an economic layer on top of the bridging protocol between THORChain and other blockchains, such that the economic incentives ensure that nodes will always lose more than they gain when attacking. These economic incentives allow for a robust and resilient bridging protocol, that is permissionless, but safe. THORChain Validators choose to sync and report on the latest chainstate for external bridged chains in the hope they are selected for a quorum of nodes that can be party to bridge wallets and earn on exit fees. Failing to report the correct chainstate, or by broadcasting nuisance transactions will see them kicked from the quorom. Not all THORChain Validators may be part of the quorum for a bridged chain, but they will all have access to the full quorum list and be fully aware of the nodes that are. Additionally, all nodes in quorum are always THORChain validators, so the quorum is a compliant sub-set of the consensus group that anyone can join. The quorum works in tandem with the THORChain Validators to ensure that bridge assets enter and exit securely. A healthy fee market is built around exiting, so that bridge nodes in quorum have the correct incentives to stay synced and in quorum. 

Running an external-chain node is completely voluntary and not enforced. For this reason the networking specification is vastly simplified and achieves a safer and more robust equilibrium. The following are the only rules: 

1) Validators are slashed if another validator can tender evidence of unauthorised spending
2) Validators are slashed if another validator can tender evidence of unauthorised minting
3) Validators earn on exit fees

The axioms of the Bifrost Protocol:

1) Reducing complexity reduces attack surfaces
2) The security of a bridge is probabilistic
3) Validators never trust each other
4) Validators will always act in their economic self-interests

### Definitions
`Validator` - A THORChain Validator

`Bridge_<chain>` - An external chain that Validators run fully-validated nodes for.

`Quorum_<chain>` - A quorum of nodes that agree on the latest blockHeight for the BridgeChain, and report it within a period of blocks. 

`Buffer_<chain>` - A number of blocks in the past that a node can report a blockheight for, to stay in Quorum.

`BH_<chain>` - The blockheight that the Quorum agree on 

`ConfCount_<chain>` - A hard coded number of confirmations required on the chain before it can be reported on

`ExitFee_<chain>` - The fee charged for a user to exit a bridge. 

`Pub_<chain>_<addr>` - The public address for a wallet that a Validator holds the privatekey for, for each chain. Note: UTXO coins will require a new address for each cycle. Account coins will keep the address the same. 

`Cycle_<chain>_<addr>` - The cycle time for each bridge chain, for each bridge. 

`prepBlocks` - A number of blocks that a node must report on, before joining Quorum

## Economic Incentives
Validators are fully-validating nodes that produce blocks in a round-robin fashion for THORChain. Validators will choose to run BridgeChain nodes for purely self-interested reasons:

- To allow liquidity to enter THORChain (indirectly increasing Rune value)
- To earn on Bridge Exit Fees (direct economic gain)

The protocol does not enforce 100% security guarantees on the bridges, as doing so will create complexity and increase the attack surface for bridges, ie, if you make a claim a bridge is 100% secure, it MUST be 100% secure. 

Instead, the protocol seeks to economically encourage probabilistic security by rewarding intended behaviour and punishing malicious behaviour. The claim is simply that a bridge is always insecure, but the “insecurity” of a bridge is a ratio between loss and gain incurred when attacking. Thus the security of a bridge is the inverse:

`BridgeSecurity = AttackGain / AttackLoss`

We can deterministically measure `AttackGain` and `AttackLoss` through the function of THORChain’s CLPs so we can now make a deterministic claim about a bridge’s security at any point in time, but it will never be 0%, or 100%. 

### Discouraging Malicious Behaviour

The protocol will unapologetically slash any Validator that attempts to spend or mint coins without consensus. Slashing is performed through the following process:

1) A rogue validator attempts to mint or spend without consensus
2) An observing validator collects proof of fraud and posts it on THORChain
3) If consensus on the fraud is reached, then the rogue validator is slashed and their stake is shared

Validators have to be bonded for 30 days, which reduces the nothing-at-stake attack problem. 


### Encouraging Running BridgeNodes

THORChain does not enforce running bridge nodes as this would radically increase complexity of the protocol. Instead, validators are encouraged to run bridge nodes on bridge chains and will earn on exit fees. Additionally, exit fees are set by users and will always be non-zero, so a healthy fee-market is created around exiting bridges. 

Here is the case that there are `k` THORChain Validators, with Bitcoin, Ethereum and Monero bridges. A non-zero sub-set of them run Ethereum, Monero or Bitcoin nodes, or any combination of them, and can show they do so. Some may run all three nodes.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/images/bifrost-networking1.png" width="500px" height="285px" />

If a Validator can show that they are running a node, and that they have validated up to the latest blockheight (within a buffer), then they are included in the Quorum for that bridge. A validator may be in the Quorum for all three bridges. Being included in a Quorum thus gives them a probablistic opportunity to be included as a party to each bridge, and any time a user exits an asset on that bridge they get to share in the exit fee. 

Thus the following will be achieved:
- Validators are incentivised to run nodes of their own volition, and report the latest correct blockheight
- Validators are incentivised to stay online in order to be selected as a party to a bridge
- Validators are incentivised to keep their node synced whilst they are a party in order to earn their share of the `exitFee`
- Validators are discouraged from making bridges look insecure, as this will discourage entry and exit of assets on the bridge
- Validators are encouraged to remain in a Quorum for as long as there is assets in the bridge, as they will earn on exit fees.  

### Faking Blockheight or using Remote Nodes

THORChain does not require verifiable proof that a validator is in fact running a bridge node, or that they are even validating the blockchain on that node. Validators simply need to report the latest blockheight as well as its corresponding hash, within a buffer, which they can source from their own chain-state, or a remote chain-state. 

However the validator should run a fully-validating node, as it is in their best interests to do so:

- Running a fully-validating node means they are less likely to accidentally commit fraud
- Running a fully-validating node means they can post fraud-proofs faster and earn on slashed-stake of malicious nodes

Validators will only report the correct and most recent blockheight to each bridge chain, as reporting on a fake or outdated blockheight will mean they are evicted from Quorum and cannot be selected to be party to a bridge. 


## Implementation

### Building Quorum

1) A Validator is running `thorchaind` and part of consensus for THORChain. 
2) Validators decide to add `bitcoind` and link Bitcoin to the tBitcoin CLP
3) THORChain forks (via on-chain governance) to pass (2) 
4) Validators link `thorchaind` to the location of their `bitcoind` chainstate in order to report the latest blockheight & corresponding hash that meets `ConfCount_Bitcoin` (say, 6). They are added to `Quorum_Bitcoin` with a `pending` flag. 
5) The validators report with:   `[“bitcoind”, <bh>, <hash>, <CCount>, “thorchaind”, <bh>, <hash>]`
6) Once the validators have reported for `prepBlocks`, using the THORChain blockheight to count, they are eligible to be added to active quorum state. 
7) Once a super-majority of Validators in `Quorum_Bitcoin` agree on a Bitcoin blockheight and its hash, then that is established as the most recent blockheight `BH_Bitcoin`. 
- `BH_Bitcoin` is only updated once another super-majority agreement of the next blockheight and its hash is achieved

8) All Validators reporting a blockheight within `Buffer_Bitcoin` blocks of `BH_Bitcoin` are now flagged as `active`.

9) Any validator that is in `Quorum_Bitcoin` reports:
- THORChain Public Key
- Bitcoin Public Key
- Blockheight report

The protocol can now randomly choose from the active-flagged nodes in the quorum to build up multi-sigs on Bitcoin.


### Dropping from Quorum

A Validator fails to make a Bitcoin blockheight declaration, such that the last blockheight declaration falls outside the buffer. The protocol then evicts that Validator from quorum, and enacts a bridge-cycle.

### Re-entering Quorum

After being evicted, the Validator must re-sync to the latest Bitcoin blockheight and start reporting. They can only re-enter after the mandatory wait time, using `prepBlocks` to enforce the wait period. 

A Validator may simply read and echo the latest blockheight to fake entry into the Quorum, but they will be unable to sign and post the multi-sig entry challenge, so will be skipped and their effort will be in vain. 

### Permanently Lost Quorum
A quorum will survive for as long as there is a validator syncing the chainstate to the bridged chain, and the escrowed assets have value. This is discussed below. 

### Chain Reorg or Split
The quorum reports on and establishes consensus about the latest blockheight and hash for each bridged-chain. This mechanism allows a safe handling around chain re-org and splits. The assumption to safe handling is that the nodes who participate in quorum are sufficiently geographically decentralised and do not sync to the same remote node (assuming they all run fully-validating nodes) 

#### Chain Re-org
If a chain is re-orged it means that two versions of the chain are being propagated across the Bitcoin network - this is normal. The bridge stays safe around this as long as a transaction is not processed later than `BH_Bitcoin`, which means 67% of the nodes in Quorum have seen and observed the same blockheight propagated with the required confirmation count. 

This has two levels of safety. The first is that no blockheight is reported prior to the safety limit of confirmations (6 suggested for Bitcoin) and that it has been sufficiently propagated across the network. 

If a chain is re-orged and a transaction to a bridge is rolled back, then Bitcoin will lose its peg to tBitcoin. There can be only two options to restore the peg:
1) Foundation intervention to sell assets from a common pool to restore lost assets
2) The losses are socialised:
- Losses can be recorded as an on-chain asset deficit and a portion of the Validator rewards can be accumulated to buy excess Bitcoin off the market and burnt.
- Losses can accrue in the bridges, so that only users who exit can cover the loss.
- tBitcoin can be removed from the CLP pool, so that all tBitcoin stakers lose pro-rata. 


#### Chain Split
An enforced consensus around `BH_Bitcoin` means that there will only be one blockheight and hash agreed upon, so there can be no two versions of Bitcoin. If the external chain splits, then `BH_Bitcoin` will follow the longest chain and maintain the peg and the link. 

## Exit Fees
Any validator running a bridge node that is party to an asset exit, will earn on a fee. The fee is actually partially set by the user, and is comprised of three parts:

1) Bridge-chain transaction fee
2) Bridge deficit 
3) Bridge-node exit fee

### Bridge-chain Transaction Fee
This is the fee paid by the bridge to process the outgoing transaction, and is equal to the fee paid to miners to the external chain. Fee estimators will be made available to users to select an appropriate amount, and is entirely external to THORChain.

### Bridge Deficit
Every time a bridge cycle is enacted a small amount of Bitcoin is spent as transaction fees that is not covered by any particular user. These accrue as a deficit that needs to be covered, else the true peg is lost. 
To prevent a race condition, and to prevent a loss of peg, the deficit is 100% charged to the first person to exit with an unpaid deficit. For example, if a 1m Satoshi deficit is accrued on a bridge, then the first user to exit will be charged 1m Satoshis. This has the following characteristics:

- Prevents any race conditions
- Always covers the peg
- Discourages small-value exits (but does not prevent them)

### Bridge-node Exit Fee
The bridge node exit fee is equal to the Bridge-chain Transaction Fee + Bridge Deficit. If the users pays 1sat/byte for a Bitcoin exit, then 1sat/byte is added and paid proportionally to bridge nodes. Additionally, if there is a 100k Satoshi deficit, then another 100k Satoshi is added and paid to bridge nodes. 

Importantly, the Exit Fee is paid in tBitcoin, and accrues on the Validator’s THORChain address. This prevents complexity around the claims made to the escrowed assets. If a Validator wishes to exit their accrued tBitcoin to Bitcoin, then they must use the bridge as any other user. 

The has the following desired characteristics:
- The user makes the choice about what to pay bridge nodes 
- If a user wishes to exit quickly, they will pay a higher bridgechain miner fee 
- If a user wishes to exit promptly, they will cover any deficit on the bridge (and not wait for another user to cover it first)

This Exit Fee solves two problems about the short and long term incentives in a bridge:
1) Validators are incentivised to join Quorum and earn fees in the short-term whilst a bridge has high economic activity.
2) Validators are economically incentivised to run fully-synced bridge nodes for as long as assets are in the bridge, and have value.

In fact, the higher the economic activity on a bridge, the more validators will try to join Quorum and increases the security of the bridge. Additionally, the longer a bridge goes without economic activity, the higher the deficit reward becomes, increasing the incentives to keep the bridge synced and running.

This also solves the problems around a stagnate bridge or dead chain. A stagnate bridge will slowly accrue a deficit. If the deficit in a bridge accumulates, it increases the potential payouts to any bridge nodes that are party to the time of exit. As such, more nodes will join Quorum, increasing the security of that bridge, and all others. 

Additionally, if the chain dies and the assets lose value, then the bridge nodes will eventually drop out of Quorum (from sheer economic disinterest) until a single validator remains to claim the remaining worthless assets and without the risk being slashed (as no other validator can report them). 

As a further edge case, a bridge is still safe and will remain available even though it may contain no assets. As an example, there was 10m Satoshi on a bridge the last time a user exited, but after 1000 cycles, all of it was consumed in miner fees. The bridge no longer has any assets, but since other bridges continue to have assets with value, then more than one Validator will always be in the quorum to discourage theft. 

### Fee Market
Thus users wishing to exit will be part of a healthy fee market that achieves a stable equilibrium: 

- If they want their outgoing transaction to be processed fast, they will pay a higher miner fee, and thus a higher exit fee
- If they do not wish to wait for another user, they will pay any deficit in the bridge to exit
- If they have a large amount of asset to exit, they will choose a bridge which has accumulated a lot of bitcoin, and thus cover any deficit
- If a bridge has high economic activity, then nodes will join quorum
- If a bridge stagnates or a bridge chain dies, then eventually the bridge will be safely neutralised

## Publishing Fraud Proofs
Fraud proofs are one of the most important aspects to maintain the robustness of the Bifrost Protocol. There are two types of fraud proofs:

1) Unauthorised spend of bridge assets
2) Unauthorised mint of THORChain assets

### Unauthorised Spend
A user who wishes to exit a bridge performs the following transaction on THORChain, called an exitRequestTx:

`exit[<coin>, <amount>, <bridge>, <destinationAddr>, <fee>]`

This `exitRequestTx` is immediately stored on-chain, where the only validation required is to check that the user was able to sign the transaction with the privatekey of the address sending the coins. 

Once published on-chain, bridge nodes will be in possession of the `exitRequestTx` (instantly final) and any one of them will initiate the spend transaction by gossiping their signed spend transaction, based on the user’s request. The spend transaction will include a hash of the `exitRequestTx`. The rest of the nodes will also gossip their spend transaction and collect incoming signatures. Any gossiped `spendTx` that are received by Bridge nodes with the following conditions, will result in the offending node being booted from Quorum, and the `spendTx` marked as invalid:

1) Missing an accompanying `exitRequestTx` hash
2) `SpendTx` differing from `exitRequestTx` in any manner (addr, amount, fee)

This will prevent an incorrect (or fraudulent) `spendTX` from being propagated. 

If any of the nodes observe another node publishing on the bridgeChain a `spendTx`, the following is true:

1) Any node can prove a fraud exists (`onChainSpendtx` differs to `exitRequestTx`)
2) Any node can prove the fraudulent actor (the signed tx matches the previously declared Bitcoin Public Key). 

>Note: the assets are still safe as the multi-sig bridge wallet requires a supermajority to sign on-chain. 

The bridge nodes will then sign and gossip a `FraudProofTX` with the `onChainSpendtx` and the `exitRequestTx` to the Quorum. Each node in the Quorum that receives a `FraudProofTX` will add their signature if they agree, until 67% of the signatures are collected. At this point any node can publish the final signed `FraudProofTx` on-chain to THORChain. The block producer for that block (which may not have visibility of the BridgeChain) will be able to validate the `FraudProofTX` as long 67% of the quorum have signed (which they do have visibility of). The block will be fully-validated by 67% of THORChain validators, which includes a transaction to slash the offending Node and distribute to all non-offending Validators. 

### Unauthorised Mint

A user who wishes to enter a bridge performs a transaction on the bridgeChain that demarcs on chain the THORChain destination address (using OP_Return, ExtraData, PaymentID or a smart contract field). 
Any bridge node in the Quorum can then observe the transaction and gossip a signed `mintRequestTX`, which includes the `BH_Chain` that the observed the transaction in:

`mint[<coin>, <destAddr>, <BH_chain>, <txhash>]`

Each bridgeNode will collect the incoming `mintRequestTx` and add their signature if the following is true:

1) The Blockheight referred to is not later than the `BH_Chain` established in the quorum
2) The `txHash` has the minimum number of confirmations
3) The `mintRequestTx` matches the `userTx`

If the above is not true, then the node marks the `mintRequestTX` as invalid and boots the node from the Quorum. 

If 100% of the Quorum agree and add their signatures, then any node can collect the fully signed `mintRequestTX` and publish on-chain (THORChain). The `mintRequestTX` will validate if:

1) 100% signed from Quorum
2) `mintRequestTx` matches `userTX`

As all THORChain Validators will have awareness of Quorum and will not validate the block if (1) or (2) is not true, it is not possible to mint fake coins without full Quorum support, and thus consensus on THORChain. 

## Conclusion

We have proposed an effective chain-agnostic bridge protocol that uses full THORChain network security to allow assets to be moved seamlessly on and off the ecosystem. By randomly reshuffling the validators used in multi-signature accounts and having multiple bridges with different security and performance metrics, each bridge maintains optimal performance whilst being supervised with full protocol security. If a validator attempts to spend locked assets they are slashed and the seized assets can be used to restore the assets. With the use of CLP price feeds the protocol can become self-aware of the risks of each bridge, and make adjustments to signature requirements. Tokens on THORChain gain low fees, on-chain liquidity, and a superior trading environment. All assets can be used by traders to stake inside CLPs to earn on liquidity fees, so tCoins will likely be more valuable than original assets. Tokens created on THORChain can also be easily deployed on multiple other chains but recovered easily.
The Bifröst Protocol builds an economic layer on top of the bridging protocol between THORChain and other blockchains, such that the economic incentives ensure that nodes will always lose more than they gain. These economic incentives allow for a robust and resilient bridging protocol, that is permissionless, but safe. THORChain Validators choose to sync and report on the latest chainstate for external bridged chains in the hope they are selected for a quorum of nodes that can be party to bridge wallets and earn on exit fees. Failing to report the correct chainstate, or by broadcasting nuisance transactions will see them kicked from the quorom. Not all THORChain Validators may be part of the quorum for a bridged chain, but they will all have access to the full quorum list and be fully aware of the nodes that are. Additionally, all nodes in quorum are always THORChain validators, so the quorum is a compliant sub-set of the consensus group that anyone can join. The quorum works in tandem with the THORChain Validators to ensure that bridge assets enter and exit securely. A healthy fee market is built around exiting, so that bridge nodes in quorum have the correct incentives to stay synced and in quorum. 

## References

[1]Anon, n.d. POA Network. [online] POA Network: public Ethereum sidechain with Proof of Authority consensus by independent validators. Available at: <https://poa.network/>

[2]Anon, n.d. Total Transaction Fees in USD. [online] Blockchain. Available at: <https://www.blockchain.com/charts/transaction-fees-usd>

[3]Unchained, C., 2018. The Technicals of Interoperability-Introducing the Ethereum Peg Zone. [online] Cosmos Blog. Available at: <https://blog.cosmos.network/the-internet-of-blockchains-how-cosmos-does-interoperability-starting-with-the-ethereum-peg-zone-8744d4d2bc3f>

[4]Cosmos, 2018. 2-way permissionless pegzones. [online] Cosmos Github. Available at: https://github.com/cosmos/peggy/tree/master/spec
