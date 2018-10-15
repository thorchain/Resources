# Bifröst Protocol Networking

## Securing the Bifröst Protocol without hard security guarantees

devs@thorchain.org
V0.1 October 2018

### Abstract 
>

### Document Set
This is a sister paper to the main Bifröst Protocol whitepaper:

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol/whitepaper-en.md)
Secure and fast cross-chain bridges for THORChain.

## Overview

[Introduction](#introduction)	


[Conclusion](#conclusion)	


## Introduction

Bifrost Networking Spec
Overview
The Bifrost Networking Spec relies on economic incentives to encourage as many Validators to run external chain nodes “BridgeNodes” and be available to become party to bridge wallets.

Running an external-chain node is completely voluntary and not enforced. For this reason the networking specification is vastly simplified and achieves a safer and more robust equilibrium. The following are the only rules: 

Validators are slashed if another validator can tender evidence of unauthorised spending
Validators are slashed if another validator can tender evidence of unauthorised minting
Validators earn on exit fees

The axioms of the Bifrost Protocol:

Reducing complexity reduces attack surfaces
The security of a bridge is probabilistic
Validators never trust each other
Validators will always act in their economic self-interests

Definitions
Validator - A THORChain Validator

Bridge_<chain> - An external chain that Validators run fully-validated nodes for.

Quorum_<chain> - A quorum of nodes that agree on the latest blockHeight for the BridgeChain, and report it within a period of blocks. 

Buffer_<chain> - A number of blocks in the past that a node can report a blockheight for, to stay in Quorum.

BH_<chain> - The blockheight that the Quorum agree on 

ConfirmationCount_<chain> - A hard coded number of confirmations required on the chain before it can be reported on

ExitFee_<chain> - The fee charged for a user to exit a bridge. 

Pub_<chain>_<addr> - The public address for a wallet that a Validator holds the privatekey for, for each chain. Note: UTXO coins will require a new address for each cycle. Account coins will keep the address the same. 

Cycle_<chain>_<addr> - The cycle time for each bridge chain, for each bridge. 

Economic Incentives
Validators are fully-validating nodes that produce nodes in a round robin fashion for THORChain. Validators will choose to run BridgeChain nodes for purely self-interested reasons:

To allow liquidity to enter THORChain (indirectly increasing Rune value)
To earn on Bridge Exit Fees (direct economic gain)

The protocol does not enforce 100% security guarantees on the bridges, as doing so will create complexity and increase the attack surface for bridges, ie, if you make a claim a bridge is 100% secure, it MUST be 100% secure. 

Instead, the protocol seeks to economically encourage probabilistic security by rewarding intended behaviour and punishing malicious behaviour. The claim is simply that a bridge is always insecure, but the “insecurity” of a bridge is a ratio between loss and gain incurred when attacking. Thus the security of a bridge is the inverse:

BridgeSecurity = AttackGain / AttackLoss

We can deterministically measure AttackGain and AttackLoss through the function of THORChain’s CLPs so we can now make a deterministic claim about a bridge’s security at any point in time, but it will never be 100%, or 0%. 

Discouraging Malicious Behaviour

The protocol will unapologetically slash any Validator that attempts to spend or mint coins without consensus. Slashing is performed through the following process:

A rogue validator attempts to mint or spend without consensus
An observing validator collects proof of fraud and posts it on THORChain
If consensus on the fraud is reached, then the rogue validator is slashed and their stake is shared

Validators have to be bonded for 30 days, which reduces the nothing-at-stake attack problem. 


Encouraging Running BridgeNodes

THORChain does not enforce running bridge nodes as this would radically increase complexity of the protocol. Instead, validators are encouraged to run bridge nodes on bridge chains and will earn on exit fees. Additionally, exit fees are set by users and will always be non-zero, so a healthy fee-market is created around exiting bridges. 

Here is the case that there are K THORChain Validators, with Bitcoin, Ethereum and Monero bridges. A non-zero sub-set of them run Eth, Monero or Bitcoin nodes, or any combination of them, and can show they do so. Some may run all three nodes. 

If a Validator can show that they are running a node, and that they have validated up to the latest blockheight (within a buffer), then they are included in the Quorum for that bridge. A validator may be in the Quorum for all three bridges. Being included in a Quorum thus gives them a probablistic opportunity to be included as a party to each bridge, and any time someone exits an asset on that bridge, whilst the node is a party to it, all nodes get to share in the exit fee. 

Thus the following will be achieved:
Validators are incentivised to run nodes of their volition, and report the latest blockheight
Validators are incentivised to stay online in order to be selected as a party to a bridge
Validators are incentivised to keep their node synced whilst they are a party in order to earn their share of the exitFee
Validators are discouraged from making bridges look insecure, as this will discourage entry and exit of assets on the bridge
Validators are encouraged to remain in a Quorum for as long as there is assets in the bridge, as they will earn on exit fees.  

Faking Blockheight or using Remote Nodes

THORChain does not require verifiable proof that a validator is in fact running a bridge node, or that they are even validating the blockchain on that node. Validators simply need to report the latest blockheight as well as its corresponding hash, within a buffer, which they can source from their own chain-state, or a remote chain-state. 

However the validator should run a fully-validating node, as it is in their best interests to do so:

Running a fully-validating node means they are less likely to accidentally commit fraud
Running a fully-validating node means they can post fraud-proofs faster and earn on slashed-stake of malicious nodes

Validators will only report the correct and most recent blockheight to each bridge chain, as reporting on a fake or outdated blockheight will mean they are evicted from Quorum and cannot be selected to be party to a bridge. 


Implementation
Building Quorum

A Validator is running thorchaind and part of consensus for THORChain. 
Validators decide to add bitcoind and link Bitcoin to the tBitcoin CLP
THORChain forks (via on-chain governance) to pass (2) 
Validators link thorchaind to the location of their bitcoind chainstate in order to report the latest blockheight & corresponding hash that meets ConfirmationCount_Bitcoin (say, 6). They are added to Quorum_Bitcoin with a “pending” flag. 
The validators report with:   [“bitcoind”, <bh>, <hash>, <CCount>, “thorchaind”, <bh>, <hash>]
Once the validators have reported for prepBlocks, using the THORChain blockheight to count, they are eligible to be added to active quorum state. 
Once a super-majority of Validators in Quorum_Bitcoin agree on a Bitcoin blockheight and its hash, then that is established as the most recent blockheight BH_Bitcoin. 
BH_Bitcoin is only updated once another super-majority agreement of the next blockheight and its hash is achieved
All Validators reporting a blockheight within Buffer_Bitcoin blocks of BH_Bitcoin are now flagged as “active”.
Any validator that is in Quorum_Bitcoin reports:
THORChain Public Key
Bitcoin Public Key
Blockheight report
The protocol can now randomly choose from the active-flagged nodes in the quorum to build up multi-sigs on Bitcoin.
Dropping from Quorum

A Validator fails to make a Bitcoin blockheight declaration, such that the last blockheight declaration falls outside the buffer. 
The protocol evicts that Validator from quorum, and enacts a bridge-cycle.

Re-entering Quorum

After being evicted, the Validator must re-sync to the latest Bitcoin blockheight and start reporting. 
They can only re-enter after the mandatory wait time, using prepBlocks to enforce the wait period. 

A Validator may simply read and echo the latest blockheight to fake entry into the quorum, but they will be unable to sign and post the multi-sig entry challenge, so will be skipped and their effort will be in vain. 
Permanently Lost Quorum
A quorum will survive for as long as there is a validator syncing the chainstate to the bridged chain, and the escrowed assets have value. This is discussed below. 

Chain Reorg or Split
The quorum reports on and establishes consensus about the latest blockheight and hash for each bridged-chain. This mechanism allows a safe handling around chain re-org and splits. The assumption to safe handling is that the nodes who participate in quorum are sufficiently geographically decentralised and do not sync to the same remote node (assuming they all run fully-validating nodes) 

Chain Re-org
If a chain is re-orged it means that two versions of the chain are being propagated across the Bitcoin network - this is normal. The bridge stays safe around this as long as a transaction is not processed later than BH_Bitcoin, which means 67% of the nodes in Quorum have seen and observed the same blockheight propagated with the required confirmation count. 

This has two levels of safety. The first is that no blockheight is reported prior to the safety limit of confirmations (6 suggested for Bitcoin) and that it has been sufficiently propagated across the network. 

If a chain is re-orged and a transaction to a bridge is rolled back, then Bitcoin will lose its peg to tBitcoin. There can be only two options to restore the peg:
Foundation intervention to sell assets from a common pool to restore lost assets
The losses are socialised:
Losses can be recorded as an on-chain asset deficit and a portion of the Validator rewards can be accumulated to buy excess Bitcoin off the market and burnt.
Losses can accrue in the bridges, so that only users who exit can cover the loss.
tBitcoin can be removed from the CLP pool, so that all tBitcoin stakers lose pro-rata. 


Chain Split
An enforced consensus around BH_Bitcoin means that there will only be one blockheight and hash agreed upon, so there can be no two versions of Bitcoin. If the external chain splits, then BH_Bitcoin will follow the longest chain and maintain the peg and the link. 

Exit Fees
Any validator running a bridge node that is party to an asset exit, will earn on a fee. The fee is actually partially set by the user, and is comprised of three parts:

Bridge-chain transaction fee
Bridge deficit 
Bridge-node exit fee

Bridge-chain Transaction Fee
This is the fee paid by the bridge to process the outgoing transaction, and is equal to the fee paid to miners to the external chain. Fee estimators will be made available to users to select an appropriate amount, and is entirely external to THORChain.

Bridge Deficit
Every time a bridge cycle is enacted a small amount of Bitcoin is spent as transaction fees that is not covered by any particular user. These accrue as a deficit that needs to be covered, else the true peg is lost. 
To prevent a race condition, and to prevent a loss of peg, the deficit is 100% charged to the first person to exit with an unpaid deficit. For example, if a 1m Satoshi deficit is accrued on a bridge, then the first user to exit will be charged 1m Satoshis. This has the following characteristics:

Prevents any race conditions
Always covers the peg
Discourages small-value exits (but does not prevent them)

Bridge-node Exit Fee
The bridge node exit fee is equal to the Bridge-chain Transaction Fee + Bridge Deficit. If the users pays 1sat/byte for a Bitcoin exit, then 1sat/byte is added and paid proportionally to bridge nodes. Additionally, if there is a 100k Satoshi deficit, then another 100k Satoshi is added and paid to bridge nodes. 

Importantly, the Exit Fee is paid in tBitcoin, and accrues on the Validator’s THORChain address. 
This prevents complexity around the claims made to the escrowed assets. If a Validator wishes to exit their accrued tBitcoin to Bitcoin, then they must use the bridge as any other user. 

The has the following desired characteristics:
The user makes the choice about what to pay bridge nodes 
If a user wishes to exit quickly, they will pay a higher bridge chain miner fee 
If a user wishes to exit promptly, they will cover any deficit on the bridge (and not wait for another user to cover it first)

This Exit Fee solves two problems about the short and long term incentives in a bridge:
Validators are incentivised to join quorum and earn fees in the short-term whilst a bridge has high economic activity.
Validators are economically incentivised to run fully-synced bridge nodes for as long as assets are in the bridge, and have value.

In fact, the higher the economic activity on a bridge, the more validators will try to join quorum and increases the security of the bridge. Additionally, the longer a bridge goes without economic activity, the higher the deficit reward becomes, increasing the incentives to keep the bridge synced and running.

This also solves the problems around a stagnate bridge or dead chain. A stagnate bridge will slowly accrue a deficit. If the deficit in a bridge accumulates, it increases the potential payouts to any bridge nodes that are party to the time of exit. As such, more nodes will join quorum, increasing the security of that bridge, and all others. 

Additionally, if the chain dies and the assets lose value, then the bridge nodes will eventually drop out of quorum out of disinterest until a single validator remains to claim the remaining worthless assets and without the risk being slashed (as no other validator can report them). 

As a further edge case, a bridge is still safe and will remain available even though it may contain no assets. As an example, there was 10m Satoshi on a bridge the last time a user exited, but after 1000 cycles, all of it was consumed in miner fees. The bridge no longer has any assets, but since other bridges continue to have assets with value, then more than one Validator will always be in the quorum to discourage theft. 

Fee Market
Thus users wishing to exit will be part of a healthy fee market that achieves a stable equilibrium: 

If they want their outgoing transaction to be processed fast, they will pay a higher miner fee, and thus a higher exit fee
If they do not wish to wait for another user, they will pay any deficit in the bridge to exit
If they have a large amount of asset to exit, they will choose a bridge which has accumulated a lot of bitcoin, and thus cover any deficit
If a bridge has high economic activity, then nodes will join quorum
If a bridge stagnates or a bridge chain dies, then eventually the bridge will be safely neutralised











Publishing Fraud Proofs
Fraud proofs are one of the most important aspects to maintain the robustness of the Bifrost Protocol. There are two types of fraud proofs:

Unauthorised spend of bridge assets
Unauthorised mint of THORChain assets

Unauthorised Spend
A user who wishes to exit a bridge performs the following transaction on THORChain, called an exitRequestTx:

exit[<coin>, <amount>, <bridge>, <destinationAddr>, <fee>]

This exitRequestTx is immediately stored on-chain, where the only validation required is to check that the user was able to sign the transaction with the privatekey of the address sending the coins. 

Once published on-chain, bridge nodes will be in possession of the exitRequestTx (instantly final) and any one of them will initiate the spend transaction by gossiping their signed spend transaction, based on the user’s request. The spend transaction will include a hash of the exitRequestTx. The rest of the nodes will also gossip their spend transaction and collect incoming signatures. Any gossiped spendTx’s that are received by Bridge nodes with the following conditions, will result in the offending node being booted from Quorum, and the spendTx marked as invalid:

Missing an accompanying exitRequestTx hash
SpendTx differing from exitRequestTx in any manner (addr, amount, fee)

This will prevent an incorrect (or fraudulent) spendTX from being propagated. 

If any of the nodes observe another node publishing on the bridgeChain a spendTx, the following is true:

Any node can prove a fraud exists (onChainSpendtx differs to exitRequestTx)
Any node can prove the fraudulent actor (the signed tx matches the previously declared Bitcoin Public Key). 

Note: the assets are still safe as the multi-sig bridge wallet requires a supermajority to sign on-chain. 

The bridge nodes will then sign and gossip a FraudProofTX with the onChainSpendtx and the exitRequestTx to the Quorum. Each node in the Quorum that receives a FraudProofTX will add their signature if they agree, until 67% of the signatures are collected. At this point any node can publish the final signed FraudProofTx on-chain to THORChain. The block producer for that block (which may not have visibility of the BridgeChain) will be able to validate the FraudProofTX as long 67% of the quorum have signed (which they do have visibility of). The block will be fully-validated by 67% of THORChain validators, which includes a transaction to slash the offending Node and distribute to all non-offending Validators. 

Unauthorised Mint

A user who wishes to enter a bridge performs a transaction on the bridgeChain that demarcs on chain the THORChain destination address (OP_Return, ExtraData, PaymentID or a smart contract field). 
Any bridge node in the Quorum can then observe the transaction and gossip a signed mintRequestTX, which includes the BH that the observed the transaction in:

mint(<coin>, <destAddr>, <BH_chain>, <txhash>]

Each bridgeNode will collect the incoming mintRequestTx and add their signature if the following is true:

The Blockheight referred to is not later than the BH_Chain established in the quorum
The txHash has the minimum number of confirmations
The mintRequestTx matches the userTx

If the above is not true, then the node marks the mintRequestTX as invalid and boots the node from the Quorum. 

If 100% of the Quorum agree and add their signatures, then any node can collect the fully signed mintRequestTX and publish on-chain (THORChain). The mintRequestTX will validate if:

100% signed from Quorum
mintRequestTx matches userTX

As all THORChain Validators will have awareness of Quorum and will not validate the block if (1) or (2) is not true, it is not possible to mint fake coins without full Quorum support, and thus consensus on THORChain. 








