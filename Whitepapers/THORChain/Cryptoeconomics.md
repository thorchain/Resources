---
****************************************************

THIS DOCUMENT IS ARCHIVED AND NO LONGER APPLICABLE

****************************************************
---

# THORChain Crypto-economics

## An analysis of the economics around the proposed Rune token.
devs@thorchain.org
V0.1 November 2018

### Abstract 
>We attempt to analyse the economics around the Rune token in order to determine the potential behaviour of actors who will participate in the network on launch. Rune is bonded by validators that produce blocks for the network, as well as serving as the on-chain liquidity currency. Validators are paid in block rewards and transaction fees, whilst liquidity fees are paid to liquidity stakers. Validators also opt-in to become bridge nodes and earn on exit fees from bridges. The security of the network and the bridges are determined primarily by the value of Rune locked by the validators, but a tight coupling exists between the value of assets moved across to the network and the value of the Rune token. 

### Document Set
The following whitepapers should be read in conjunction:

- [THORChain](https://github.com/thorchain/Resources/tree/master/Whitepapers/THORChain) (this paper)
A lightning fast decentralised exchange protocol.

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol)
Secure and fast cross-chain bridges for THORChain.

## Overview

The function of the Rune token and its staking mechanisms are first described. Using real-world data from other staking coins, meaningful assumptions are then made about how the Rune token will be used in the network. Various scenarios are then explored in order to discover the potential equilibrium states that the network may converge to.

## THORChain's Security

THORChain is a decentralised exchange protocol that is built to allow performant, useable and secure trading of trustless digital assets. THORChain’s crypto-economic model must allow it to achieve optimal network security and ideal asset security. Network security in this context is a characteristic of the network in being safe from byzantine actors, whilst asset security relates to there sufficient deterrents against stealing escrowed assets on cross-chain bridges. 

### Staking and Network Security

THORChain is a proof-of-stake network that relies on a number of validators bonding Rune collateral in order to become eligible to earn block rewards from the network. Since a BFT algorithm is used (Tendermint) that relies on synchronicity, the number of validators must be capped in order to reach finality in each round of block proposals. 

Matching the Cosmos architecture, it is expected to have 100 validators on launch of the network, increasing over a number of years to a larger (but capped) number. 

Since the consensus is a BFT algorithm, it is fault tolerant to a maximum of (N/3 -1) byzantine validators. Since the validators require stake, and assuming that 100% of the circulating supply can be staked, the network is optimally secure if more than 67% of the supply is locked, since a byzantine actor would not be able to purchase enough tokens to take over (N/3 -1) of the validator slots. Since the only way to incentivise the locking up of tokens (take them out of circulating supply) in typical BFT networks is via validator bonding, typical BFT networks make efforts to incentivise the locking of at least 67% of the tokens. 

[Cosmos’ proposed design](https://blog.cosmos.network/economics-of-proof-of-stake-bridging-the-economic-system-of-old-into-the-new-age-of-blockchains-3f17824e91db) is to target a 67% threshold in order to achieve optimal security by coupling emission with the proportion staked. If below 67% then inflation is driven to 20%, if above 67%, then it is driven to 7%. We look to compare this with THORChain’s proposed design of a flat 2-5% emission to validators. 

### Staking and Asset Security

THORChain must not only achieve optimal network security (protection from byzantine actors) but it must also achieve ideal asset security to protect its cross-chain bridges that will be holding external assets. By design, THORChain’s bridges are permissionless and opt-in to validators in order to protect the network from censorship and oppression. Additionally, it is not possible to involve full network security on the bridges due to limitations on external bridges (as an example, [Bitcoin multi-signatures have limitations around 15](https://bitcoin.stackexchange.com/questions/23893/what-are-the-limits-of-m-and-n-in-m-of-n-multisig-addresses/28092), far lower than the number of validators securing THORChain). 

Thus instead of hard security guarantees, the bridging protocol uses economic incentives to ensure that the assets held on bridges are always safe. Safety in this case is determined by enforcing a net economic loss on an attacking actor, and the safety threshold is the degree between potential gain and potential loss. Achieving ideal asset security is defined as ensuring that the value of stake bonded by the set of validators guarding a bridge is always twice as high than the value of the assets held on the bridge. 

In order to achieve this design, the protocol must know the conversion ratio between the value of bridge assets and its own staking token. Additionally, since the same validators that secure the network are also the validators that secure the bridges, then we can begin to describe the relationship between asset security and network security in THORChain. 

## Rune Staking

In THORChain there will be four ultimate use-cases for the Rune token. 

1. Rune is bonded to Validators to earn block rewards. 
2. Rune is circulating freely as a medium of exchange.
3. Rune is staked in liquidity pools to earn on liquidity fees.
4. Rune is not circulating at all.  

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure1.png)

The incentives to bond tokens with Validators are to earn block rewards that will be emitted as part of the token’s inflation schedule, as well as any transaction fees collected (including trading and exit fees). The incentives to stake Rune in liquidity pools are to earn on liquidity fees, which are proportional to volume across pools, transaction sizes and existing liquidity depth. 

Since the value of the Rune in the liquidity pools must equal the value of the assets held on the other side of the pools, we can immediately see how the total value of Rune now becomes coupled to the total value of the assets circulating in its network. Since the pooled Rune is a subset of the total circulating Rune, there is a leveraged coupling between the value of on-chain assets, network security and finally asset security. To determine the characteristics around the network and asset security of THORChain we must make meaningful assumptions to the proportion that will be allocated to each use-case. 

## Real-world analysis

### Proof of Stake

We look at the typical staked portion of assets from 15 known large-cap PoS coins, sorted by proportion locked. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure2.png)

The average proportion of staked assets is in the 30-60% regime, with average being 46% staked with an inflation of 30% and an ROI for nodes at 32% annual. It is noted that these coins are not true proof-of-stake networks since most have components of the block reward being issued to proof-of-work mining or governance/founder allocations. If 100% of the block reward was issued to validators then the ROI would be roughly double (or the annual inflation would have to halve). 

Interestingly, only ZCoin and GinCoin achieved optimal Byzantine security of greater than 67%. It is known that ZCoin has high [founder participation](https://zcoin.io) in node staking, whilst GinCoin has the highest inflation of all sampled coins at 126%. 
  
The five coins with low inflation schedules (less than 10%) did not in particular seem to be representative of low on-chain participation. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure3.png)

Charting the proportion locked with both inflation and node ROI we notice that there seems to be little correlation between the proportion of assets locked and inflation schedules below 20%. Above 20% there may be a small amount of correlation between increasing inflation schedules and increasing proportion of assets locked.

### Liquidity Pools

Currently the only token that uses liquidity pools is the Bancor Network Token. [BNT currently has 25%](https://bancor.network) of its supply locked in its liquidity pools, but it is worthy to highlight that BNT has no incentives for external staking. Projects that list on Bancor’s liquidity network supply the liquidity themselves. 

It would be reasonable to assume that by adding permissionless and clear incentives to staking in liquidity pools, then participation would improve to the same proportion that proof-of-stake bonding typically is, since it has the same economic incentives. 

### Not Circulating

Of all distributions involving permissionless assets there is a portion that does not participate in any form of the economy. For Bitcoin this is estimated to be close to 25% of the circulating supply of which at least 6% (1.1m) are coins attributed to Satoshi’s wallets and may never move. Thus we can assume that 5-10% of coins of any distribution are removed from supply from loss or simple stagnating possession. 

## Rune Analysis

Thus we can now make assumptions about how Rune will be positioned in the THORChain network using the analysis above. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure4.png)

If it is considered that the proportion staked achieves roughly the average of 50%, then the remaining 50% will be in pools, freely circulating, or not circulating at all. Taking the number of validators to be 100, we can peg that the average bond size will be 5m Rune. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure5.png)

If liquidity staking unlocks latent liquidity that would not normally be utilised, and follows the same pattern of behaviour of validator staking then half of the remaining liquidity would be in the pools. If 10% of the supply is removed from circulation then this would leave 15% to be freely circulating. 

For a byzantine attacker to attack the network, they would need to accumulate 25% of the supply `(50% * 1.5 / 3 ) = 250m` Rune. We will discuss the attack vector shortly. 

We can also form a position on the total value of the Rune network with the assumptions above. In this case 25% of the supply will be bonded to 50% of the on-chain assets, which means that as a minimum, the total value of the fully-diluted supply of the network will be double the value of assets. If $10m of assets are held on bridges, then as a minimum the network will be $20m. 

### Relationship with Bridges

Since THORChain is a cross-chain protocol with bridges that hold assets, we are readily concerned with asset security. We choose a safety threshold of 2 on all bridge assets, that is, there must be twice the amount of Rune securing a bridge than there is in the value of the asset. 

Since the protocol is aware of the prices of all bridged assets as a virtue of its liquidity pools, we price the bridge assets in Rune at all times. We also assume the protocol is able to detect when a a bridge approaches its security threshold and split the assets up across two bridges. 

We take three pools (Bitcoin, Ethereum, Monero) of roughly 500m in Rune value across them. We assume the signature requirements of all bridges are `10 / N`, with more than N validators available in each bridge chain’s quorum. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure6.png)

We can determine each bridge’s safety factor by dividing the total value of stake held by bridge nodes with the value of assets on the bridge. Anything higher than 1 is safe, with 2 being the ideal target. 

If we assume that 50% of these assets are staked into liquidity pools, then we can describe the coupling between total bridge assets, the value of all Rune bonded to validators and then finally the maximum permissible value of individual bridges based on signature requirements and the safety factor.

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure7.png)

This model does not take into account any speculative or intangible value attributed to the Rune token. Instead it only attempts to base the Rune value with the value of assets staked in its pools. Assigning any speculative Rune value would only serve to increase favourably the metrics above. 

### Pool Economic Activity

Since the incentives for staking arise from economic activity, we attempt to form a position on what would likely be the level of staking participation. We assume that any actor who can stake can also bond, so we can suppose that they will position their assets between staking and bonding depending on the potential ROI available. 

From real world analysis of liquidity networks and decentralised exchanges we determine the following numbers:

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure8.png)

Thus we arrive at the conclusion that 10% of Rune’s total supply will be circulating as daily trade volume, with total transaction numbers comparable to an exchange with network value around $10m.  We can deduce the daily earnings from the liquidity fee formula.

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure9.png)

Thus to target a range of 20-25% of all supply held in liquidity pools, the Rune’s inflation schedule should give a ROI to bonders of more than 3%. If ROI is higher in the pools then there will be movement from bonding to the pools, and vice versa. 

## Network Attack Vectors

For a byzantine attacker to attack the network, they would need to accumulate 25% of the supply `50% * 1.5 / 3 ) = 250m` Rune.

They have two options: buy discretely from the freely circulating supply, or buy publicly off the pools. 

### Buying discretely

We consider that this is the first option the attacker would take. At worse case scenario we can assume the attacker could discretely buy one third of the freely circulating supply without impacting the token’s market - 50m Rune. 

Thus the attacker would need to accumulate another 200m Rune from the pools.

### Buying Off Pools

Since the pools are located on the THORChain network, the attacker would first need to move over an equivalent value in assets to purchase 200m Rune from the pools. The activity in the pools is mathematically governed, so we can estimate ahead of time the outcome of purchasing 200m Rune off the pools using the output equation.

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure10.png)

To remove 200m Rune from the pools, the attacker would need to place in 4 times the amount of paired assets. Since the pools are coupled mathematically, the price action on the asset would cause the value of Rune to increase by 25 times.

### Flow on Effects

Since the Rune market is very liquid with Rune always able to be bought and sold, the dynamics of an attacker causing a price action of 25X would have the following effects:

The bridges would increase in security by 25 times due to the rise in value of the stake holding the assets. 
The value of the block rewards and transaction fees paid to validators would increase in value 25 times. This large multiple in incentives would attract further delegation from Rune holders, increasing the amount of Rune the attacker would need to acquire. This positive feedback loop would continue until the attacker cannot collect enough Rune from markets to conduct an attack. 

## Bridge Attack Vectors

### Overview of Bridges
The bridges are secured by opt-in validators that choose to maintain bridges to earn on exit fees. They form a compliant “quorum” of validators that are cycled through the multi-signatures protecting the bridge assets. There must always be equal or more validators available in quorum than that is required for each multi-signature, else the signature requirements for each bridge will reduce. 

Since bridge assets are cycled regularly, a network fee is charged to bridge assets to cover mining fees. Over time this reduces fungibility of assets that is restored by exit fees. Additionally, if a user wishes to exit a bridge they must also pay for the cost of mining their exit transaction. 

Thus exit fees are twice the sum of:

1. External mining fee (used to pay external network fees)
2. Bridge deficit (used to restore 100% fungibility of bridge assets)

Half of the exit fee remains in the bridge asset pool to cover the mining fee and bridge deficit, whilst the other half is paid to all the validators that are part of the bridge quorum at the time of exit. Thus if a bridge has high economic activity, then exit fees will attract a growing number of opt-in validators who run full-nodes and join bridge quorum. 

Thus there are two ways to attack a bridge:

1. Manipulate the on-chain value of the bridge asset down and steal low-valued assets
2. Steal assets from a bridge with low signature requirements

### Manipulating On-chain Asset Value

In the first case, a group of complicit validators manipulate the price of an asset down by continually selling it across its pool. If the value of the asset in two separate bridges approaches a safety factor of 4 (based on its on-chain value) then two bridges are consolidated into one. This means that the *actual* value of the asset now equals the value of the stake used to secure it. If the complicit validators once again manipulate the asset down by another factor of 2, then bridges are once again consolidated. At this point the *actual* value of the asset is twice as valuable as the value of the stake used to secure it, and the complicit validators can now steal it. They will lose their stake, but they will exit with a net gain. 

![](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images-c/figure11.png)

However there are two balancing effects that will prevent this occurring. The first is that the complicit validators will expose themselves to counter-arbitrage as the market tries to restore the price of the on-chain asset. Since the pools are permissionless, anyone can arbitrage the market in their own self-interest. Secondly, as the arbitrage occurs, market actors find themselves in possession of cheap on-chain assets which they now want to sell on external markets to realise their gain. If this occurs they are incentivised to move assets off the THORChain network, reducing the amount of the assets in the bridges. 

Thus assuming the bridges and the pools are always permissionless, then no actor could ever manipulate the prices of assets against the market. As a further effect, the manipulation would cause a spike in economic activity on the bridges, incentivising quorum participation and actually strengthening the security of the bridge. 

### Stealing assets with low signature requirements

A second attack vector may occur if bridge’s economic activity slows down and quorum participation reduces to the point that a very small number of signatures can be attained for bridge wallets. If economic activity on a bridge reduces then the flow on effect will be that bridge deficits grow over time, and thus the potential exit fee increases significantly. As the potential exit fee increases, it incentives quorum participation again, restoring security. 

If the exit fee is not perceived to have value, then so too, the bridge assets are not perceived to have value and there is no incentive to attack and risk the loss of stake. 

Thus we conclude that low-value assets held on low-signature bridges will never create the incentive for an attack.

## Summary

We have analysed wider market behaviours on PoS networks to make assumptions about how the THORChain network may incentivise participating actors. 

We find that it is likely that 50% of Rune will be bonded to validators, with 25% held in staking pools. An equilibrium will occur where bonding and staking will settle depending on economic activities. If an inflation rate of 3% is targeted, then no more than 20-25% of Rune will be held in pools.

We found that as a minimum the total value of Rune will be leveraged 2:1 with the value of the bridge assets, and found that overall bridge asset security increases as more assets enter the ecosystem. 

We explored attack vectors on network security and found that the byzantine threshold is thus 25% of the entire supply, but an actor attempting to acquire that amount will cause a price action of 25X, which is likely to increase bonding participation. This will in turn reduce the supply the attacker can accumulate, and increase the byzantine threshold out of reach of the attacker. 

We also looked into attack vectors on bridges and found that as long as the protocol performs as designed to continually move assets to bridges with security factors between greater than 2, then there is no plausible mechanism to steal assets with value.
