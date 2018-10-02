# ASGARDEX

## A lightning fast decentralised exchange built on the THORChain protocol.
devs@thorchain.org

V0.1 September 2018

### Abstract 
>ASGARDEX is the first decentralised exchange to be built on THORChain, intended to break feature-parity with the world's top centralised exchanges, at the same time as being more secure, reliable and powerful than any exchange before it. The features of ASGARDEX are not limited to it, instead being indicative of the powerful underlying capabilities of THORChain itself. Due to protocol-level developer inventives it is expected that there will be hundreds of exchanges, wallets and payment services deployed on THORChain. 
ASGARDEX will allow any token and coin to be traded and swapped in a completely self-sovereign and trustless environment, with no limits or account control. ASGARDEX will be highly performant, processing trades inside a second, as well as being deeply liquid with on-chain incentivised liquidity pools. Traders will be able to trade securely, coin and token holders will be able to stake in liquidity pools to earn a return, arbitrage agents will be able to earn whilst arbitraging across liquidity pools, and users will be able to swap assets instantly from decentralised applications. 
Being primarily an exchange, ASGARDEX will have native support for limit and market orders with advanced trade types and management. ASGARDEX will also feature leveraged margin trading, lending, indexes, derivatives, asset shorting and assets swaps. ASGARDEX supports fixed supply, variable supply, non-fungible tokens and security tokens.
ASGARDEX is part of the ambitious THORChain project and will be actively developed in accordance with public development milestones. 

### Document Set
The following whitepapers should be read in conjunction:

- [THORChain](https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/whitepaper-en.md)
A lightning fast decentralised exchange protocol.

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol/whitepaper-en.md)
Secure and fast cross-chain bridges for THORChain.

- [Flash Network](https://github.com/thorchain/Resources/tree/master/Whitepapers/Flash-Network/whitepaper-en.md)
A layer 2 Network for instant asset exchange on THORChain.

- [Yggdrasil Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Yggdrasil-Protocol/whitepaper-en.md)
Dynamic multi-set sharding for THORChain.

- [Æsir Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/AEsir-Protocol/whitepaper-en.md)
A self-amending forkless consensus algorithm for THORChain. 

## Overview

[Introduction](#introduction)	
- Exchanges
- Decentralised Exchanges
- ASGARDEX

[Incentivised On-chain Liquidity](#Incentivised-on-chain-liquidity)
- Slip
- Fees
- Arbitrage
- Staking
- Balancing Stake
- Withdrawing

[Advanced Features](#advanced-features)
- Asset Swaps
- Cross-token Pairs
- Index and Baskets
- Lending
- Leveraged Margin Trading
- Shorting Assets
- Trade Types
- Stablecoins

[Multi-chain and Multi-token Support](#Multi-chain-and-Multi-token-Support)

[Permissionless and Private Access](#Permissionless-and-Private-Access)
- THORChain Accounts
- FIX 4.4 Protocol ABI
- Anonymity

[Conclusion](#conclusion)	

[References](#reference)

## Introduction

### Exchanges
One of the most important advents of the cryptocurrency economy is the crypto exchange. While digital assets are useful on their own, exchanges are needed so that creators, consumers, investors, and traders can transfer these assets to one other openly and freely. As digital assets reach market equilibrium prices, all market participants benefit from clearing markets that are otherwise either unavailable or not at true equilibrium due to interference from governments, regulators, or logistical barriers. Venture capitalists, service providers, employees, founders, and other company stakeholders benefit from freely available liquidity that is not locked up in arcane or encumbering pre-IPO restrictions. This is true for tokens regardless of whether they are asset-backed, service-backed, or infrastructure-backed. In the asset-backed case case, cryptocurrency exchanges allow for easier transactions of securities that are currently more costly in terms of time and money due to reconciliation requirements that are natively on the blockchain and otherwise handled by back-office workers.
Cryptocurrency exchanges allow people increased flexibility in using the cryptocurrency of their choice for each individual transaction they undertake. They can focus on a few key markets that capture their consumption needs. 

As specialized cryptocurrencies gain traction, users can segment their wealth in specific token ecosystems. They can reward these ecosystems with more of their wealth for positive governance and pro-social activity, forcing all token ecosystems to compete with each other to provide the best economy for users. This competition is only feasible with the proliferation of exchanges that enable fast, secure, reliable transfers of wealth from one token to another.

Finally, exchanges allow traders to profit from predicting the future behavior of markets. As global wealth becomes increasingly concentrated in the hands of the few, so too go the resources needed to predict the future of markets and thereby gain additional wealth. Because cryptocurrency markets are new and lightly regulated, large incumbents have less of an overwhelming advantage over lay-traders in forming accurate, profitable hypotheses. Skilled traders can participate more directly in the rewards of insightful analysis without tethering themselves to major institutions, which can disperse market gains.

### Decentralized Exchanges. 
Of course, the above is only true to the extent that other sources of power are also decentralized. One clear target for decentralization is the exchange itself. Centralized exchanges can hold undue power over users. This allow them to engage in many of the regressive practices that cryptocurrency enthusiasts dislike in the fiat world.
Some hold users’ funds without explanation or recourse. A decentralized alternative will allow users to maintain self-sovereign rights to their assets at all times. Some centralized exchanges bar users, currencies, or transactions because of local law in their domicile. A decentralized alternative has no central operator upon which a nation state can impose restrictions. A decentralized alternative with public code can benefit from perpetual public security audits and publicly driven code changes. All things being equal, decentralized exchanges (DEXes) more adequately capture the ethos of cryptocurrencies than centralized ones. 
All things are not yet equal, however. Decentralized exchanges need feature and performance parity with centralized exchanges to successfully compete. Binance, one of the most popular centralized exchanges, processes over 1 million transactions per second. Forkdelta, a popular decentralized exchange, routinely has transactions that take minutes. Bittrex, a popular centralized exchange, has a well-designed UI honed by design specialists and a simple-to-use UX. Barterdex, a DEX, has an obtuse UI and requires several cumbersome steps in its UX. Centralized cryptocurrency exchanges pride themselves on listing a myriad of different token types. Most modern DEXes operate within a single cryptocurrency’s ecosystem (for example, stellar for Stellardex and ether for Etherdelta) allowing users to trade only tokens in one protocol.
To avoid some of these constraints, some DEXes take partial steps to toward decentralization but centralize key aspects of the system (like order books and matching engines). This is a nice attempt but these services do not provide censorship resistance and so do not fulfill the promise of decentralization. The market needs a decentralized exchange that adheres to the core tenets of decentralization and still provides a world-class exchange experience that meets the feature and performance standards set by centralized exchanges.

### ASGARDEX
ASGARDEX is a new approach to this problem by building a blockchain itself to be the DEX, rather than building a DEX on an existing protocol. In this way the protocol can be optimised to solving the problems solely limited to asset exchange as opposed to adding unnecessary features such as turing-complete scripting languages. Additionally the DEX is not constrained by the limitations of the underlying protocol, as the protocol can be readily changed with on-chain governance to match the pace of innovation in the industry. 

A trade is simply an atomic swap between at least two assets and two accounts at a pre-agreed price and within any time condititions. With this focus, THORChain is being developed and ASGARDEX will be the first exchange to be built on the protocol. More accurately, ASGARDEX is simply a user interface that is built with THORChain as the decentralised backend. As THORChain is completely open-source, it is imagined that there will be hundreds of exchanges also built on THORChain, contributing to the same liquidity and user-base. 

ASGARDEX is part of much larger picture; that by creating a near-perfect environment for trading, the inevitable flow of liquidity and users will allow the protocol to support a much larger payment network, which will use the underlying liquidity and trustless price feeds of the protocol. This is the Flash Network, [discussed at length here]() and it will allow anyone to pay in any currency instantly, at near-perfect price conversions.

The features that ASGARDEX will bring to life on THORChain will allow it (and any other exchange built on the protocol) to break feature-parity with current centralised exchanges, as well as powering the next generation of wallets and payment services. 

The cornerstone features of ASGARDEX:
- Incentivised On-chain Liquidity
- Advanced Trading
- Multi-chain and Multi-token support
- Permissionless Access

These features are discussed at length in this paper. 

## Incentivised On-chain Liquidity

Creating the incentives for on-chain liquidity is a cornerstone feature of THORChain. Instead of digital assets being held in accounts that don’t contribute to market liquidity, THORChain encourages users to stake their assets in on-chain continuous liquidity pools that add to market liquidity and earn their holders a return. 

Continuous liquidity pools (CLPs) work by bonding two assets to each other in a pool in terms of their value. Each time an asset is placed in the pool, the opposite asset is emitted in a ratio that matches the corresponding change in value. As such, trading across a pool creates a price slip which serves as the basis for arbitrage, staking incentivisation and price predictability in the pool. The price slip is mathematically defined, and works to ensure four aspects:

1) Value always matches across the pool, no matter how large or small an asset pool is. 
2) The trade price is mathematically defined and predictable ahead of time. 
3) Those staking the underlying liquidity always earn a return on a trade. 
4) Those arbitraging across the pool earn a return on a trade. 


On-chain liquidity pools hold bonded assets at a 1:1 ratio in total value, where `value = price * amount`. If price shifts in the surrounding market then anyone can arbitrage the pool to correct the price imbalance by purchasing cheap assets (or selling expensive assets). As a result, the pool will always hold bonded assets at a price that matches external markets, else an opportunity exists for someone to gain from. As the assets are bonded together, liquidity is termed as “continuous”, as there will always be a sell-side for each buy order, no matter the price.

 <img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-1.png" width="500px" height="250px" />
 
*Figure: Rune bonded to Token at 1:1*


Here 100 Rune are bonded to 100 Tokens at a 1:1 value ratio `1 * 100 : 1 * 100`. If the token value in the wider market increases to 1.20 Rune, then the value of the Token pool is now 120 Rune. Since the assets are bonded, the 120 Tokens are collateralised by 100 Rune, which is at a 20% discount to the wider market. Anyone can now purchase Tokens at a discount by simply placing enough Rune in the pool to correct the pool value ratio back to 1:1. In this case they would place 10 Rune, receive 9 Tokens (at a 10% discount) and the pool value ratio would be back to 1:1, where `1 * 110 = 1.20 * 91`. 

 <img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-2.png" width="500px" height="250px" />
 
*Figure: Rune bonded to Token at 1:0.8 after market movement*
  
  
  >Note: pricing here is in Rune (ᚱ), assuming a constant Rune price, but the CLP functioning as described is irrelevant to the price of Rune, and may in fact be based in $ or BTC, assuming proper $:ᚱ or BTC:ᚱ price discovery is in place. For the rest of the paper, ᚱ will be used interchangeably with $, with the assumption that the $:ᚱ market is an efficient market. 
 
### Slip

Compared to the Bancor Protocol [1], which mints “smart tokens” in a ratio that is bonded to wider market circulating supply, THORChain CLPs do not mint, and instead enforce a price slip on traded assets proportional to the trade size and existing collateral. 

Take the CLP above with 100 Rune `RUNE` and 100 Token `TKN`. If a trade of 10 Rune was made `txR` the corresponding emission of token `txTKN` is defined as follows:

`txTKN / TKN = txRUNE / RUNE`

`txTKN = (txRUNE / RUNE ) * TKN`

However, this bond is purely linear and does not enforce a price slip. As such a 100 Rune trade would simply empty the entire pool of Token, leaving a `1 * 200 : 1 * 0` value ratio, which does not respect the Rule (1) above. As such, we need to price each asset in terms of the price ratio after the trade. This is done by simply adding the incoming transaction to the total Rune collateral. 

`txTKN = (txRUNE) / (RUNE + txRUNE)) * TKN`

### Fees

However, this provides no return to underlying stakers as arbitrage from one side to the other will remove any gains, which does not respect Rule (3). As such, we add a small fee that has the following characteristics:

- Proportional to trade size, where the higher the trade, the higher the fee. 
- Exponentially & inversely proportional to collateral, where the higher the slip the higher the fee.
- Placed on the side where there is buy pressure. This prevents stakers receiving assets that are under sell pressure, and inadvertently adding to the sell pressure when they withdraw assets. 

These characteristics ensure that stakers are incentivised the most where there is high volume and low liquidity, and that if they withdraw their gains they add a price-corrective function to the market. The equation is:

`txFee = tradeSlip ^ 2 * txTKN`

where `tradeSlip = (txRune - (txTKN * (RUNE/TKN))) / txRune`

The final bonding curve is:

`tokensEmitted = txTKN - txFee`

`tokensEmitted = txRUNE) / (RUNE + txRUNE)) * TKN - ((txRune - (txTKN * (RUNE/TKN))) / txRune) ^ 2 * txTKN`

Or visually:

![tokensEmitted = \frac{tx_{R}}{RUNE + tx_{R}} * TKN - ( \frac{ tx_{R} - \frac{RUNE}{TKN}}{tx_{R}}) ^{2} * tx_{TKN}](https://latex.codecogs.com/gif.latex?%5Cdpi%7B100%7D%20%5Cfn_cm%20%5Clarge%20tokensEmitted%20%3D%20%5Cfrac%7Btx_%7BR%7D%7D%7BRUNE%20&plus;%20tx_%7BR%7D%7D%20*%20TKN%20-%20%28%20%5Cfrac%7B%20tx_%7BR%7D%20-%20%5Cfrac%7BRUNE%7D%7BTKN%7D%7D%7Btx_%7BR%7D%7D%29%20%5E%7B2%7D%20*%20tx_%7BTKN%7D)

This bonding curve enforces a predictable price slip and fee on any trade, as well as ensuring that values on both sides of the pool are always adhered to; Rule (2).

The graph below indicates how this bonding curve works. As the amount transacted into the pool increases, the slip + fee increases towards 100% (where the trader receives nothing as it is all consumed by fees). The characteristics above ensure that the trade amount is always deterministic, and that the stakers receive a return. Additionally, there is no upper limit of how much can be transacted into the pool, and that the staked liquidity is protected even with very large transactions. With huge sell pressure on one side of the pool, the stakers on the buy side asset receive both asset appreciation, as well large amounts of fees. If they withdraw these fees they provide a price-corrective function and reduce the sell-side pressure. This protects the pool from a black swan event. 

With low amounts (<10% of the liquidity depth), stakers can earn up to roughly 0.1% on each trade, which matches the typical taker fees on exchanges.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-3.png" width="500px" height="250px" />

*Figure: The bonding curve for THORChain CLPs*


### Arbitrage

We take the case that 100 Rune and 100 Token are staked. If a trade of 10 Rune was made, the trader would experience a 10% slip, and receive 9 Tokens. Assuming the wider market does not change, an arbitrager will see the opportunity of the 10% discounted Rune, and perform a counter-trade of 9.10 Tokens to receive 9.92 Rune.

This is 8% more Rune than if they had just made an ad-hoc trade across a balanced pool. Once the counter-trade is performed the final ratio has been restored to 100.9 Rune to 100.9 Token, 1:1. The stakers receive a payout of slip - arbitrage, which is 0.08 Token and 0.08 Rune. 

The end result is that both stakers and arbitragers receive a gain from maintaining the price and liquidity in the pool, with a healthy return to the arbitrager for correcting the price, and a passive return for the stakers for maintaining the liquidity. Additionally, assuming the fees are not withdrawn, the liquidity depth in the pool has increased, which is more favourable to traders. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-4.png" width="500px" height="250px" />

*Figure: Symmetrically staking in a CLP*


### Staking
Staking is an inherent and important part of the protocol and provides liquidity to all assets. Due to the way that CLPs are created alongside the instantiation of digital assets on THORChain, there will always be some liquidity depth to assets. By adding incentivisation (liquidity fees), liquidity depth can be vastly increased, increasing the usefulness and value of the protocol. 

When staking, a user can stake symmetrically or asymmetrically in the pool. Despite the method of staking, the user will always end up receiving assets on both sides of the pool. The reasons for this are twofold:

1) By receiving assets on both sides of the pool the staker is hedged against any downside on one side and thus any fees earned presents a net gain in value to the staker. 
2) Staking asymmetrically creates a price imbalance that is corrected by either arbitrage or market forces, and thus results in an accumulation of assets on the unbalanced side for the staker. 

It is in the interest of the user to stake symmetrically in the pool. This is performed by making two staking transactions into the pool (either simultaneously or very immediately) with both assets. 

`stake(bal_RUNE, bal_TKN)`

Where `bal_RUNE / RUNE = bal_TKN / TKN`

If the staker performs an asymmetric stake-in they will cause a price imbalance that will immediately be arbitraged out, and will incur slip that is experienced on their own assets. After the arbitrage is finished, the asymmetric staker will have both assets, minus whatever slip they experienced, plus any fees they accumulated during the arbitrage. 

Here shown for the scenarios of staking from 0% to 100% symmetrically (ie 0 Rune and 10 Token, or vice versa). As shown, it is most favourable to stake 100% symmetrically; with an asymmetric stake causing a 2.5% reduction in assets. This is more pronounced at lower liquidity depths.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-5.png" width="500px" height="250px" />

*Figure: Loss of assets after assymetrically staking and being arbitraged*


### Balancing Stake

An asymmetrical stake is balanced for the staker by the protocol simply accruing assets on their empty balance until the values on both sides match:

`priceRune * RUNE = priceToken * TKN`

Where `priceRune` is always 1, and `priceToken = 1- (TKN / RUNE)`

Incoming assets are accrued by arbitrage or natural market trades. As a result of the ensuring trade volume across the pool caused by the price imbalance, fees accumulate and the pool actually ends up with more assets than a pure symmetrical stake, shown below. Additionally, the arbitrager makes a small return on taking advantage of the price imbalance.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-6.png" width="500px" height="250px" />
<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-7.png" width="500px" height="250px" />

*Figure: An assymetric stake-in and consequential arbitrage*


### Withdrawing

Assets held in pools are entirely self sovereign at all times. Users can withdraw interest or their principle at any time by signing an on-chain transaction with their private key. They can withdraw symmetrically or asymmetrically; however an asymmetric withdrawal will create a price balance that will naturally be arbitraged out, returning the staker to a symmetrical position. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-8.png" width="500px" height="250px" />
<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-9.png" width="500px" height="250px" />

*Figure: An assymetric stake-out and consequential arbitrage*


Here is the case of a staker removing their stake asymmetrically, causing a price imbalance on the pool. Immediately an arbitrager restores the pool balance to earn cheap tokens, and thereby returning the pool ratio to 1. In this case all stakers earn on the liquidity fee, and the asymmetric staker is balanced out. 

### Benefits

The benefits for this are ground-breaking when it comes to a digital asset exchange. 

- With deep on-chain liquidity, any wider market volatility creates arbitrage opportunities which slows down market movements, as each price fluctuation needs to “move” through the pool. The depth of the liquidity pool has a dampening effect on asset volatility and translates to a transparent and observable “market inertia”.
- With liquidity incentivisation strategies, anyone can stake assets in liquidity pools and earn on liquidity fees as the market moves from side to side. This liquidity is normally held on exchange cold wallets or user wallets and never contributes to market liquidity.
- As volume grows across a trading pair, this will translate to increased volume over the pool and a disbursement in fees to liquidity stakers. Receiving a passive return on staked assets will incentivise more liquidity to be staked which will increase the liquidity depth of the pool, improving trading experience. 
- With an observable and deep liquidity depth, large orders can be processed on-market without creating adverse reactions, as staked liquidity can’t be faked, and any price movements can be immediately arbitraged out by anyone.
- With permissionless staking and arbitrage mechanisms, it become very difficult to manipulate asset prices; as any manipulated asset immediately exposes the manipulator. 
- With on-chain liquidity pools, the liquidity pool ratio becomes a fair, open and transparent market price for the asset, which can be processed in on-chain transactions. 
- On-chain trustless price feeds enable the protocol to build a wide array of advanced features not previously possible. 

## Advanced Features

### Asset Swaps

Continuous liquidity pools allow for fast and predictable asset swaps from Rune to any asset in the THORChain ecosystem. Users simply place one asset in a pool, and immediately receive the other. CLPs can also be linked, allowing any token to be swapped with another via two CLPs. As an example, a user can swap TokenA with TokenB by simply trading from TokenA into Rune and then from Rune into TokenB. In this case, there would be a small slip into Rune, as well as TokenB, but this is known ahead of time. 

Asset swaps can be performed away from the trading environment, such as wallets and marketplaces, allowing users to hold any token they wish, and use just-in-time conversion to support the accepted currency. 

In such a scenario, a user may hold a certain token, TokenA, but a merchant only accepts TokenB. Instead of the user holding TokenB, they simply make a payment through the THORChain ecosystem, which performs the swap from TokenA to TokenB almost instantly and at a market price that is fair, trustless and resistant to manipulation. 

### Cross-Token Pairs

Linking CLPs in this manner also allows an exchange to support any number of trading pairs. For example, Rune is traded with TokenA on the RUNE:TKNA pair. If the user wishes instead to trade on the TKNB:TKNA pair, but does not hold TokenB, their trade makes use of the RUNE:TKNB CLP to trade into and out of TokenA without ever holding TokenB. In this case the user would incur a small liquidity fee in addition to any maker/taker fees added by the exchange.


### Indexes and Baskets

A powerful feature of the CLP is that multiple assets can be grouped on one side of the pool, and linked as collateral to a third asset, which represents the homogenous collection of all grouped assets. If a user wishes to create a token index of 2 grouped coins, they simply create a new token, but link the token to the CLPs of the two coins of choice. Now, whenever a trade is placed into the CLP Basket, the Rune is swapped into the two grouped assets via the relevant CLPs of those assets. The assets are retained in the pool, and a third token is atomically minted at a 1:1 ratio of the assets, and issued to the user.

If a user wishes to sell their index token against the CLP, they simply send in their tokens, which are immediately destroyed in the pool. The corresponding amount of underlying assets (pro-rata to the index) are then sent via the external CLPs to swap into Rune, which is then issued to the user. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/images/asgardex-10.png" width="500px" height="250px" />

*Figure: A token Index*


Such a pool would present a number of arbitrage opportunities in order to keep the index re-balanced with respect to the total value of the grouped assets and collateralised Rune. A good example of this is are stablecoins. A basket of stablecoins using assets from different implementations would then be far less risky than any individual stablecoin. The pool would be constantly re-balanced by arbitrage agents, which in itself provide better assurances of a recovery from natural market fluctuations around the assets. 

### Lending

Lending in crypto-currencies is always fully collateralised (often largely over-collateralised) which is used to under-write the asset that is issued to the borrower, typically a stablecoin. If the underlying asset-base decreases in value, then the collateral is liquidated to keep the collateralization ratio constant. THORChain can support such a scheme trustlessly by simply referring to the CLP price feed between the collateralised asset and the lended asset, and making liquidations on behalf of the lender. As the CLPs are actually hyper-efficient markets (there is no spread), the collateralisation ratio can be reduced to 100%. Additionally, low volume coins can also be lended against, by tracking the liquidation price of the asset if it were 100% liquidated in a single trade. 
As an example TokenC is a low volume coin, with 10,000 staked in a pool with 100 Rune. The conversion is 100:1. If a borrower held up 10,000 of TokenC as collateral, then they can be permitted to borrow only as much as if it were liquidated in a single trade, which is deterministic (around 37.5 Rune). If the price of TokenC increases to 120:1, then the collateral is only worth 36 Rune (after liquidation). As such, around 200 TokenC is progressively liquidated on behalf of the borrower in order to reduce the debt down to 36 Rune. 
These automatic liquidations can be performed by validators, but they can also be performed by relayers who scan for bad debt and perform the liquidation transactions in return for a small fee. 

Using the same mechanism, interest payments can also be performed trustlessly on the borrowed asset, by withdrawing from the collateral and converting to Rune. 

Blackswan events may still occur, but the automatic and progressive liquidation of collateral against a CLP with a deterministic liquidity depth far reduces the risk. The main risk comes from stakers in liquidity pools removing their assets which will reduce the liquidity depth sharply, causing large amounts of collateral to be liquidated fast. However liquidation events from TokenC to Rune cause earnings to accrue quickly (in Rune) for any remaining stakers. Due to the design of  CLPs, staking is most favourable in high volume low liquidity pools, so the large liquidation event should encourage more staking, in return restoring the liquidity depth. 


### Leveraged Margin Trading

Leveraged margin trading crypto-currencies involves putting up collateral and receiving assets on a multiple (leverage) that can be traded. As an example, if a trader puts up 1 Bitcoin to trade at 100X leverage, they are lended 100 Bitcoin. If the borrowed asset incurs a loss, the collateral is automatically liquidated to cover the borrower, at the correct ratio. The borrowing, interest payments and automatic liquidation of such assets is the same for Lending above.

Validators or relayers track downstream assets that have been borrowed, and constantly refer to the CLP price in order to determine the asset value. In this way they can deterministically ascertain if a trade has resulted in a loss of value, regardless of the asset itself. If this is the case then they can liquidate part of the asset holdings in order to protect the collateral. 


### Shorting Assets

An asset is shorted by a trader borrowing the asset from a lender, then immediately selling it (for $USD). If the asset decreases in value (in $USD), they can buy it back for much less, and then pay out their debt to the borrower. Any difference between the initial sell price and the final re-purchase price is a net gain for the shorter. 

THORChain’s CLPs permit shorting of any asset by combining the Lending & Liquidation features above, in addition to the functionality of the CLPs. Further, they can also allow shorts to be priced in any currency of value to the trader, not just $USD or BTC. For example, a trader may short TokenD against TokenB. In this case, they borrow TokenD and sell it for TokenB. If TokenD reduces, they can buy it back with their TokenB holdings and pay out their debt. 

Traders can also perform leveraged shorts by trading with a leverage ratio. In this case their gains (or losses) are amplified, but borrowings can be liquidated as required to prevent a loss to the lender. 

### Trade Types

Fill or Kill
All or Nothing
Immediate or Cancel

### StableCoins 

THORChain’s macro vision is to create a highly liquid payment network built on a completely trustless structure. An imperative feature for mainstream adoption are stablecoins that are pegged to existing fiat currencies such as USD, YEN, EURO and AUD. The first way to achieve this is to simply tokenise existing stablecoins, while the second is to support the development of stable coins on the platform. The first will naturally be easy if the bridges in `§ 8` are built. 
THORChain has all the required features to create StableCoins with auditable supply and collateral using a Variable Supply token and its CLP and full Validator Set participation. The following would be the process:
- A tokenChain `tUSD` is created by the Validator Set. A `Rune:tUSD` CLP is created that is variable supply, requiring `2 / 3` signatures from Validators, or by a on-chain smart contract.  
- At each round, Validators (on smart contract) propose a price `$` that the Rune is valued at. They can infer this by any means possible, most likely by reading APIs off a conglomerate of external exchanges of Rune. Validators can also nominate Rune pricing by watching Bitcoin pricing off external exchanges, and converting to Rune price by the `Rune:tBTC` price feed. 
- At each round `n + 1` the median of nominated prices is stored in the block. Outliers may invoke slashing rules to penalise poor price nomination. 
- As Rune price fluctuates, `tUSD` supply in the CLP is negatively changed by the Validators. As an example, Rune price decreases by `2%`, so `tUSD` Supply increases by `4%`. This causes `tUSD` to become inflated and cheap. 
- Self-interested arbitrageurs will then send in Rune to buy cheap `tUSD`, until `tUSD` returns to `1:1` backed in Assets.
- Additionally, the liquidity fee will increase the staked collateral in the CLP to match volume and reduce slip. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images/figure38.png" width="900px" height="246px" />

*Figure: Collateralized on-chain StableCoins.* 

This process can be repeated for any other fiat currency and is very simple. Once an external price of Rune is set (in the fiat currency), then the protocol deliberately creates arbitrage opportunities by influencing money supply to attract third parties to act in a self-interested manner to restore any price imbalance. By requiring full Validator Set participation in the price nomination the price of the StableCoin can be relied upon with the same assurance as the entire protocol itself.  The pricing/supply feedback loop can be modified to reduce damping and price sensitivity. 

### Token Baskets  & Indexes
A THORChain can go further than a single token being represented in a CLP. With a combination of CLP scripts and trustless price feeds, token baskets and indexes can be created and represented by a single asset. This allows users and traders to carry a single token and know that the token’s price trustlessly represents other assets.   
A new tokenChain `TKNIndex` can be created that represents `TKN1`, `TKN2`, and `TKN3`, where the `TokenData` and `CLPData` for `TKNIndex` is linked to the CLPs of the indexed assets. The `TKNIndex` CLP is bonded to include liquidity in all other CLPs so the price of `TKNIndex` is thus representative of the linked assets. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images/figure40.png" width="350px" height="223px" />

*Figure: Token Baskets*

## Multi-chain and Multi-token Support

THORChain will implement the [Bifröst Protocol](https://github.com/thorchain/Resources/blob/master/Whitepapers/Bifrost-Protocol/whitepaper-en.md) to allow support of all tradable assets, no matter the originating chain. The Bifröst Protocol is essentially a trust-minimised bridging protocol that does not require any change to the bridged chain, and uses the security of the THORChain ecosystem to ensure that assets never present a viable honeypot to any adversary. This allows assets to be moved seamlessly on and off the ecosystem.
Bridges to THORChain are supervised by THORChain validators in multi-signature accounts, but by randomly reshuffling the signatories and having multiple bridges with different security and performance metrics, each bridge maintains optimal performance whilst being supervised with full protocol security. If a validator attempts to spend locked assets they are slashed and the seized assets can be used to restore the assets. With the use of CLP price feeds the protocol can become self-aware of the risks of each bridge, and make adjustments to signature requirements. 
Tokens on THORChain gain low fees, on-chain liquidity, and a superior trading environment, and all assets can be used by traders to stake inside CLPs to earn on liquidity fees, so `tCoins` will likely be more valuable than original assets. Tokens created on THORChain can also be easily deployed on multiple other chains but recovered easily. 


## Permissionless and Private Access

### THORChain Accounts

There is no restriction on who can generate a THORChain account and trade from it, and trading has no limits. Indeed, there is no restriction on who can enter the ecosystem via the Bifrost Protocol, and no limits on those assets movements. As there is no restriction on who can become a THORChain validator, and there are correct incentives for a large group of active and standby validators, there are several layers of redundancy to the ecosystem. THORChain is a protocol that simply allows for the transfer and trading of assets, it does not enforce any level of account management, control or censorship.

In order for juridictionally compliant exchanges to be built on THORChain, it is up for the exchange itself to add a KYC/AML layer and an account management system. The exchanges best placed to do this are those with fiat on/off ramps. 

Additionally, in order for Security Tokens to be traded and supported on THORChain, it is up for the exchange layer to add an account whitelisting feature to ensure tokens can only be bought/sold between registered users. 

### FIX 4.4 Protocol ABI

FIX 4.4 is the Foreign Information Exchange protocol built for trading desks to place trades such as indications, orders and executions in a standardised and efficient way across a network. It has been adopted as the standard electronic trading protocol and THORChain intends to build for FIX 4.4 compatibility to allow liquidity and access to institutional traders and investors. 
The following message types are commonly used in FIX 4.4:
- Indication of Interest
- Quote Request
- Quote Response
- Quote
- New Order
- Execution Report
- Allocation Instruction 
- Allocation Report
- Trade Capture Report
- Application Blockchain Interface (ABI) bridges the protocols and allows orders to be generated on Layer 1 or Layer 2 depending on liquidity needs and order types. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images/figure41.png" width="350px" height="400px" />

*Figure: FIX 4.4 implementation proposal.*

### Anonymity

THORChain can implement the [ZeroCoin Protocol](http://zerocoin.org/media/pdf/ZerocoinOakland.pdf) to allow re-spawning of accounts to prevent linkability. Tokens are destroyed in one transaction and then trustlessly spawned in a new coinbase transaction. To prevent temporal analysis tracking identity, Alice can specify a block height delay to the re-spawn of her account. To prevent temporal analysis identifying immediately appearing tokens with immediately disappearing tokens; a variable wait time can be set to increase noise.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/THORChain/Images/figure39.png" width="350px" height="298px" />

*Figure: Anonymity.*


## Conclusion

ASGARDEX represents a new frontier of exchanges, with permissionless access, on-chain incentivised liquidity and advanced trading features. THORChain, the protocol that it is built on, will power the next generation of exchanges, wallets and payment networks.


## References

[1] Bancor Whitepaper, Eyal Hertzog, Guy Benartzi, Galia Benartzi, 2018,  https://storage.googleapis.com/website-bancor/2018/04/01ba8253-bancor_protocol_whitepaper_en.pdf
