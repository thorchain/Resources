# Æsir Protocol; on-chain governance

## Enabling a forkless self-amending ledger for THORChain. 
devs@thorchain.org

V0.1 - July 2018

### Abstract 
>We propose an effective on-chain governance mechanism to be implemented in THORChain. Validators in THORChain stake Rune and auction their way into the Validator Set of `100`. Each Validator proposes blocks for the network, committed once `67%` supermajority is attained. As part of proposing a block, each Validator can suggest core changes to the protocol, which includes consensus rules, architecture changes, state changes, token structure changes, on-chain commands or governance changes. Consensus on the change is decoupled from consensus of the block. The proposing Validator begins running the software on a separate SideNet, whilst still securing the MainNet. The SideNet is a side-fork of MainNet and the proposing validator continues to sync all MainNet blocks to SideNet. All other Validators start signalling support of the change in the affirmative or negative. Signalling the affirmative requires integrating the diff patch and joining SideNet; allowing the opportunity to perform live tests of the new code. Validators are penalised if no signal is committed inside a set period of blocks (time). Once a change attains `67%` consensus the SideNet immediately becomes the canonical chain with no disruption to users and the old chain is abandoned. Anyone can delegate Runes to a Staker (prospective Validator) or a Validator itself and vote on a signal. Quadratic signalling is used to prevent plutocracy influence, where a single vote has a large comparative weight than bundled votes. The signalling threshold in a Staking Pool is `51%`, allowing individual voters the opportunity to swing a signal. Stakers can switch pools to vote, but are bound by the enforced bonding period. 

## Overview

[Introduction](#introduction)	

On-chain Governance
- The Problem	
- The Solution	
- Two-Layer Governance	

Overview	
- Economically Incentivised Governance	
- Empowering Minorities	

Implementation	
- Overview	
- Signalling a Proposal	
- TestNet	

[Conclusion](#conclusion)	

## On-chain Governance 

### The Problem

On-chain governance is a difficult problem to solve. Satoshi originally coined the term “one-CPU-one-vote” in the original Bitcoin whitepaper but this was short-lived. Mining pools split the power between miners and nodes and  GPU mining-turn-ASIC mining quickly centralised “signalling” to the point where contentious hard forks and user-activated-soft-forks are a historical threat to Bitcoin. Ethereum has similar problems, but with substantially more decentralisation. Ethereum does have on-chain governance for miners in that they vote at each block to increase the gas limit, however this is very limited. New features still require hard forks. EOS’s on-chain governance has somewhat transitioned to out-of-band governance instead whereby Block Producers (BPs) can make unilateral decisions on chain state and most governance decisions appear to come from a staffed council. EOS’s style of governance is more characteristic of representative democracy instead of direct democracy, which leads to to BPs being engaged in running popularity contests to secure votes and not actually engaging their voters in on-chain decisions. 

Even despite Ethereum’s virtual machine (EVM) allowing anyone to write generic turing-complete smart contracts and execute arbitrary code, this does not work for updating the EVM itself, let alone Ethereum node software and the protocol itself. Ethereum attempted to solve this by bringing in an “ice-age” mechanism which incrementally introduced unfavourable difficulty to the blockchain to encourage miners to migrate as one away from old chains. However, this was short-sighted and the community subsequently forked in a delay to the Ice Age.

### The Solution
Tezos and DFinity propose a new form of on-chain governance where node and virtual machine software updates are voted on by the network block producers each time they produce a block. In this way, core software and protocol-level rules can be updated dynamically. Updates are easier to write, faster to update and are much more powerful to the development of the protocol. 
Any block producer can propose a change in the core software and consensus rules structured as a simple data packet of the description, code to executed and a diff patch. All other validators vote to accept the change and if it reaches supermajority consensus the code can be immediately brought in to operation. The proposing and agreeing validators can run the already compiled core software on standby, so that once approved, the core software is live to produce the very next block. 
If on-chain governance is solved with high participation rates and effective signalling rules, then THORChain is capable of continuously updating in line with the development of technology.  The types of updates that can be rolled out are essentially unlimited and could be:
Consensus rules such as supermajority thresholds, or signalling rules (relating to on-chain governance itself)
Protocol architecture such as a change to consensus algorithms, integration of sharding, change to the blockchain structure or signature schemes. 

Native on-chain commands as discussed, whereby additional trading rules can be integrated at the protocol level.
Changes to the token structure such as supply or inflation.
Changes to state, such as amending exploited or unused accounts.  
 
## Two-Layer Governance

### Overview
Each Validator in THORChain is open to Delegators who stake with them to be part of the Validator Set. Stakers are Validators who are not yet part of the Validator Set due to not having sufficient entry stake. Delegators have the ability to re-delegate at any time. 
As such there are two levels of THORChain’s governance to cater for the needs to Delegators, Stakers and Validators, and adequately empower them all appropriately. The following are considerations:
- There must be incentives to participate in on-chain governance, or penalties for no participation. 
- Minorities must be empowered to prevent a plutocracy forming. 
- Non-voting minorities can be represented by the Validator they stake too. 
- Vote switching must be permitted for flexibility. 

The two layers are known as Voting inside of Staking Pools, and Signalling for Validators. Delegators vote on a suggested change inside a pool, whilst a Validator sums their vote and signals the final result. Signalling involves an additional process which requires the Validator to perform to ready their node to accept the new software. Voting is set with a `51%` threshold, whilst Signalling requires `67%` consensus. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/AEsir-Protocol/images/figure1.png" width="400px" height="400px" />

*Figure: On-chain voting and signalling*

### Economically Incentivised Governance
The single biggest problem to current on-chain goverance solutions is low voter turnout, which means decisions don’t actually represent the community consensus. As on-chain governance is crucial for THORChain to become a self-amending ledger with maximum community participation,  solutions to this problem will be carefully designed and implemented. There are incentives for voting participation and penalties for not signalling. 

**Incentivisation.** Since Signalling can only be performed by Validators, Rune-holders who wish to vote on governance issues have to stake Rune as part of a Staking Pool in Validators. Validators are paid block rewards, which is distributed to anyone who is staking with them. Thus Rune-holders are paid to take part in on-chain governance and help secure the network at the same time. If a proportion of a staking pool fails to vote, then the Validator will submit on behalf of them, thus voter turnout will always be 100% of staked tokens. Non-voting staked Rune-holders can at any time update their vote. This mechanism is thus a hybrid between direct and representative democracy, where the representation is only made if a direct vote is not used by its owner.

**Slashing Rules.** Signal participation is enforced by in-protocol slashing rules. Not signalling on a proposed update will result in a Validator’s stake being slashed and redistributed to other Validators who do Signal. Signalling is binary (`YES/1` or `NO/0`), so to prevent penalties Validators will immediately signal NO for all changes using their pool’s votes until they have time to consider the change or poll the community and/or their staking pool. There can be a grace period of n blocks allowing Validators time to take up a position before casting a vote. If a Validator sends a NO signal immediately, their Delegators can eventually tip the signal to YES by actively voting.  

### Empowering Minorities

**Quadratic Voting.** THORChain’s governance architecture must be carefully designed to prevent the issues associated with plutocracy influence. This can be achieved by implementing quadratic voting inside a validator’s staking pool, a well studied method specifically proposed to counter the plutocracy issue. Each member that stakes with a Validator can cast votes quadratically proportional to their stake, where each vote is a single whole unit, and increases with the number of subsequent votes. `1` vote costs `1` token, `2` votes cost `4 tokens (2 * 2)`, `3` votes cost `9 tokens (3 * 3)` etc. With this policy, casting many votes quickly becomes expensive and the most an individual is willing to pay matches the most they would be willing to pay for a single vote. This appropriately removes the biases that whales can have on Signalling, preventing a plutocracy. It also enables and empowers the minority to know their vote is meaningful and represented. 
If a Validator submits votes on behalf of non-voting stakers in their pool the vote is quadratically weighted. Thus non-voting stakers are empowered to easily swing their Validator’s final signal as their individual vote has more comparative weight than the Validator who representatively voted for them using their tokens. 

|Votes|Quadratic Weight|Running Cost
|:---|:---|:---|
1|1|1
2|4|5
3|9|14
4|16|30
5|25|55
...|...|...
22|484|3795
23|529|4324
24|576|4900
25|625|5525

*Table: Voting Weights and Costs*

In the case that a Validator was staking 5000 Rune, with 98% of that (4900 Rune) held by a single individual, with the rest (2%) made up of 100 individuals. The single individual’s vote would cost 4900 Rune and have the comparative weight of 24 individual votes. On the other hand the 100 individuals could cast with a combined weight of 100 votes, and overpower the whale. In fact the vote could be swung with 99.5% (4975) held by a whale, and 25 individuals voting against. If the whale re-staked with 10 different wallets of 500 Rune each, they could still be overpowered with 110 individuals. Each time tokens are staked they enter a bonding period enforced by the protocol, so a whale breaking up their stake can only occur once during a voting sessions (unless the voting session continues for a while). 

**Swing Voters.** As part of increasing voter turnout, minorities must feel they can sway a vote. In THORChain there are two levels of signalling; staking pools cast votes that quadratically weighted, and validators cast a single final vote that represents the majority of their pool, and this final vote is counted in a set of 100 and requires supermajority (⅔) consensus to pass. If passionate voters can not sway their pool’s final vote (insufficient support), they can switch staking pools with another validator where they are more likely to swing 51%, and change the final vote. 

Waiting Validators (who do not have sufficient stake to be in the Validator Set) can signal votes, but these are not included. Thus if minorities feel they can swing a vote by delegating stake to a waiting Validator and getting them into the Validator Set with economic power, they can also complete this action. 

**Vote Switchers.** As part of the rules of bonding, stakers who delegate their stake to a Validator have their token locked for n blocks, long enough to avoid the risks of a long-range attack. Thus voters have to commit to their pool during the course of the vote that they want to be part of. They can update their vote at any time in their pool, but if they switch staking pools to swing vote, they will likely be bound to that new pool for the duration of the vote. Vote switchers who stake with waiting validators also run the risk of on not having their vote included if there is insufficient economic power to get their chosen Validator included in the Validator Set. 


## Implementation

### Overview
There are various types of changes that can be submitted via the THORChain Improvement Proposals, TIPs and merged in by Validator Signalling. Simple changes can be integrated quickly, more complex changes go through a formal verification process which includes a process called side-forking.

**Account Changes.** Account changes, such as changing TokenData or CLPData retrospectively can also be effected by Validator Signalling. It is likely that the community would canvas the change and the Validators simply integrate it on request. These changes may not have far-reaching effects. An example of this is EIP-999 which is the Ethereum Improvement Proposal to change the contract data to recover locked funds from the Parity Multi-sig breach. The proposal itself would actually benefit the community as it is releasing funds to the people who owned them, but the hard fork to do so would damage the community. This can be avoided by using Validator Signalling.

**Protocol Config Changes.** Changes to protocol configuration such as that to do with the token supply, inflation, TNS fees, CLP liquidity fee thresholds can have wide-reaching changes, but can be integrated relatively easily. Proposing Validators simply propose the changed config which can be merged in without much overhead. It is likely that Protocol Config changes would automatically be vetoed by the Validators until the community can assess the reasons and motivations for the change. 

**Protocol Architecture Changes.** Changes to the architecture such as additional trading or account rules as well as integration of other features such as the Bifrost Protocol or Flash Network would require a formal verification process. This is known as side-forking.

**Consensus or Governance Changes.** Consensus and Governance rules relating to supermajority thresholds or signalling rules are also cornerstone to the protocol and would require extended formal verification through side-forking. 

### Signalling a Proposal
Proposals can come from the community, but it requires at least one Validator to sponsor it, so more than likely they will come from Validators themselves. `TIPs` are instructions to other Validators on how to integrate the proposed change, the code to change and a diff patch. `TIPs` are indexed and stored in the MerkleChain. 

```
[TIP#: {description, newCode, diffPatch}]
```

In each block a Validator produces they include a signal in the block header to demonstrate signalling. Signalling is as simple as specifying a `YES/1` or `NO/0` alongside the `TIP#`. 

```
{TIP#, NO} 
{TIP#, YES}
```

Once 67% of Validators have signalled in support of a TIP, then the supporting Validators can now start generating blocks with the latest software compiled and the blocks will be approved. 


### TestNet
When a Validator proposes a sweeping architectural change that requires formal verification, they participate in testing the change on the official TestNet. The proposed new software is first compiled and a new TestNet is run (as a fork or a new genesis). The SideNet is synced from the MainNet by the proposing Validator, who acts as a relayer. The Validator then submits a TIP containing a flagged blockhash from the SideNet. Validators who vote in favour of the update run the updated software and the SideNet, and submit votes also included flagged blockhashes. Whilst the SideNet is running it is formally verified as all new blocks are synced over. New features can be run If fatal errors occur
Once `67%` of Validators are running the SideNet, the SideNet becomes the canonical net, and the old net is abandoned. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/AEsir-Protocol/images/figure2.png" width="400px" height="290px" />

*Figure: A sidefork to a SideNet.*

The sponsoring Validator incurs an infrastructure cost in maintaining the SideNet, so they are unlikely to sponsor a TIP that won’t get support from other Validators, or continue maintaining a SideNet that hasn’t attracted much support. 



### Conclusion
This represents our current research on on-chain governance but additional revisions may be needed.  A carefully designed on-chain governance solution (which itself can update) will see THORChain become a self-amending ledger and increase the likelihood of persistence well into the future. 

