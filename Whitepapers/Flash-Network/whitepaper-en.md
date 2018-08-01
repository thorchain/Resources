
# Flash Network; an interoperable payment channel network.

## Allowing instant, low-fee, highly liquid and reliable asset exchange across layer 2 networks.

devs@thorchain.org

V0.1 July 2018

### Abstract 
>The Flash Network is a Layer 2 payment channel network on THORChain, allowing instant trades across all tokens and liquidity pools. Liquidity hubs anchor to price feeds and an economic model ensures always-on liquidity and high reliability of payment channels. Bridges with other Layer 2 networks such as Lightning, Raiden and Celer allow for instant exchange of any connected asset. Support for off chain trading mechanisms allow for fully featured decentralized exchanges.

### Document Set
The following whitepapers should be read in conjunction:

- [THORChain](https://github.com/thorchain/Resources/tree/master/Whitepapers/THORChain) 
A lightning fast decentralised exchange protocol.

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol) 
Secure and fast cross-chain bridges for THORChain.

- [Flash Network](https://github.com/thorchain/Resources/tree/master/Whitepapers/Flash-Network) *(this paper)*
A layer 2 Network for instant asset exchange on THORChain.

- [Yggdrasil Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Yggdrasil-Protocol)
Dynamic multi-set sharding for THORChain.

- [Æsir Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/AEsir-Protocol)
A self-amending forkless consensus algorithm for THORChain. 

## Overview

[Introduction](#introduction)	
- Payment Networks	
- Flash Network	

[Overview](#overview)	
- Mjölnir Liquidity Hubs	
- Hub Setup	
- Flash Network Incentives	
- Cross-hub Trades	
- Cross-Network Compatibility	
- Settlement	

[DEX Protocol](#dex-protocol)	
- Overview	
- Related Work	
- Layer 2 Trading	
- Cohesive Design	
- Future research and solutions	
- Liquidity Bonds	
- Advanced Trade Types	

[Conclusion](#conclusion)	


## Introduction

### Payment Networks

**Layer 2 Payment Networks**. A Layer 2 payment network sits on top of a blockchain and is composed of bi-directional payment channels. While the underlying blockchain defines and secures value, a Layer 2 Network allows trustless, scalable and unicast transactions of that value. The Lightning Network [1] on Bitcoin has been under development for several years now; and is still in nascent stages. Whilst a very promising payment technology; it is suffering from early issues of reliability and liquidity. Channels close frequently, and there is no strong economic incentive for nodes to fill channels with liquidity. 
Payment Networks. The Visa Network is one of the incumbent global payment processors; used in almost all countries, processing up to 40k transactions per second and moving $6.8tn in currencies each year. However it is centralised, expensive and uses legacy technologies with no current support for cryptocurrencies. 

### Flash Network 
**Flash Network.** The Flash Network adds an instant exchange layer on top of THORChain allowing users to instantly trade any supported asset. Economic incentives reward liquidity and reliability, creating a highly useable payment channel network. Bridges with external Layer 2 Networks such as Lightning[1] and Raiden[2] allow trades across networks. The Flash Network is built to allow seamless and instant trades between any asset on any network for the THORPayments ecosystem.  

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure8.png" width="500px" height="180px" />

## Flash Network

### Overview
In order to achieve widespread success, THORChain needs to provide a payments system that succeeds at both being a fully transparent, trustless network as well as providing simple, seamless and opaque interactions for everyday end users. Optimizing for ideal UX and performance with minimal fees and waiting times and strong payment guarantees is the goal of the Flash Network. The Flash Network will allow for instant transfers from one user to another and guaranteed payments across different token pairs with automated conversion. Additionally, beyond everyday user payments and conversions, later work will allow for it to become a fully fledged Layer 2 trading system that supports the needs of different kinds of traders and complex trades.

The Flash Network is a Payment Channel Network and DEX protocol that sits on top of THORChain and links the trustless price feed from each underlying Continuous Liquidity Pool (CLP) with a group of liquidity providing nodes (named Mjölnir). Liquidity nodes within a group open channels up with each other and fill channels with Rune and tokens they wish to support.
Payments can be made instantly and across TokenChains and facilitate high frequency trading.  
Users open channels with Mjölnir and perform flash transactions. When a flash is made, the continuous liquidity node is used to instantly trade tokens at internal pool pricing; which represents fair market pricing. Instead of performing 1:1 channel movements (such as lightning network) the Mjölnir perform channel movements at the underlying token price ratio.


<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure9.png" width="400px" height="285px" />

In this example Alice pays Bob 1 Rune, but Bob wishes to receive in TKN1, which is half the price of 1 Rune. The Mjölnir performs the swap instantly and pays out to Bob 2 TKN1. 

### Mjölnir Liquidity Hubs

Mjölnir Liquidity Hubs in the Flash Network are comprised of an unlimited amount of nodes that are incentivised to hold liquidity in channels across the hub with the RUNE token, and settle trades. Each node that joins the hub puts up a minimum amount of liquidity, which is governed by the following equation:

```minimumLiquidity=hubLiquiditynodeCount+1```

Liquidity is shared across all nodes in the hub, and all channels evenly. The nodes then collect transactional fees from trades which add to existing liquidity. Hubs that attract high amounts of trades and subsequent fees, will attract more nodes to join, which in turn increases the liquidity available. This will attract more traders. On the other hand, well-connected hubs will be expensive to join, thereby encouraging nodes to distribute their liquidity across more hubs. 
Each Liquidity Hub is connected to a single TokenChain, and has direct access to the on-chain CLP. There can be multiple Liquidity Hubs connected to the same TokenChain, thereby encouraging fee competition for each pair. 
Nodes can exit the hubs at any time; and retract their collateral and any collected fees:

```Claim=hubLiquiditynodeCount```

Traders wishing to perform instant Layer 2 trades across a pair open buy or sell channels with the hub. A sell channel is a channel filled with the TKN of the Hub; a buy channel is filled with tokens. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure4.png" width="285px" height="400px" />

When a channel is opened and an order created the Hub immediately adds outgoing liquidity to the channel. If the channel is a sell, tokens are added through a single on-chain transaction. If the channel is a buy, liquidity is added by a single on-chain transaction through the CLP to the channel. If the channel is closed, the liquidity is returned to the Hub in the reverse. To prevent denial of service and sybil attacks, opening a channel with the Hub requires a small joining fee, the amount set by the Hub.

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure5.png" width="390px" height="400px" />

Once two orders are matched, trades can performed instantly by moving the tokens in the correct sense. Trades can be partially or fully filled. 

### Hub Setup
Hubs are created when multiple nodes with sufficient liquidity connect to each other by setting up a system of direct communication channels with each other and by locking up collateral on THORChain.
Hubs can lock up collateral under different conditions to allow for different kinds of Hub setups. Initially, two core mechanisms will be explored.

### Payment Channel Driven
Hub nodes can lock up their collateral in bi-directional but unicast payment channels 1 on 1 with other hub nodes, splitting their liquidity evenly across their channels. End users will open up a single payment channel with a single hub node, tapping into the liquidity of that node. Hub nodes share knowledge of incoming transactions, incoming requests from users to open a new channel, and expected flows. They can then ensure new users are routed to open their initial channel up with a hub node that is most likely to support their liquidity needs. Additionally, if liquidity shifts on the wrong direction amongst channels belonging to a group of hub nodes, they can trade liquidity direction with each other off-chain to rebalance their channels [3].

### Sidechain Driven
Hub nodes can set up a multiparty payment channel, allowing users to join and exit at will, effectively running a small sidechain amongst themselves with a simpler and less trustless, but much more performant consensus algorithm. Hub nodes and Users then lock up their collateral on THORChain together to have those funds become part of the sidechain. In the event of malicious activity or distrust in this sidechain’s consensus, users can submit a withdrawal or fraud proof to the root THORChain and fallback to it’s more decentralized, trustless consensus to ensure the safety of their funds. Plasma/Plasma Cash[4][5] and NOCUST[6] are two examples of possible mechanisms a hub could implement, and various other additional research directions exist. 
Each Mjölnir must provide a mechanism that guarantees safety for a user’s funds in the event of malicious activity, and ensures a good UX and API, but beyond that will be free to implement the above, or own other internal mechanisms for incentivizing liquidity and reliability.

### Flash Network Incentives
Lightning and Raiden suffer from poor network incentives to collateralise channels and stay online. As a result, user experience is poor due to an unreliable network. 
Mjölnir Liquidity Nodes require a minimum amount of liquidity to join hubs, and their channel uptime is tracked. If they join a hub with liquidity and stay on-line beyond the minimum required, they will be awarded a proportion of the fees, shared across each node in proportion to their stake.  This will result in high-quality hubs, nodes and channels, massively improving the reliability of the network. 
Liquidity, once locked up, is limited and so the various Mjölnir as well as core THORChain CLPs will be competing over the same liquidity. Flash Network liquidity incentives will be balanced with core THORChain CLP liquidity incentives, ensuring that liquidity will shift towards systems to consistently meet user demand, whether that demand shifts more towards CLP trading or towards Layer 2 payments and swaps.
Hub nodes can essentially utilize on chain CLPs to swap and shift liquidity across themselves, their assets and the on chain CLP and so will periodically do this. The on chain transaction fees and slippage they incur will be taken into account by their pricing model for fees from end users, ensuring they remain profitable.
Unlike incumbent networks which suffer from the majority of their users holding tokens in personal wallets indefinitely without use, THORChain tokens will be launched with this liquidity-first mindset, encouraging users to store their tokens in either CLP liquidity or Flash Network liquidity and ensuring everyday users can easily be incentivized to and by default participate in these liquidity systems.

### Cross-hub Trades
Once two Liquidity Hubs have been formed, TKN1 and TKN2, they can be connected with fully-collateralized channels. Now, the two tokens can be traded directly with each other with the Rune acting as a settlement currency. This achieves a TKN1:TKN2 trade pair. 
Connected with the Bifrösts, this means that any supported currency can be instantly traded with each other, even across networks. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure7.png" width="500px" height="170px" />

### Cross-Network Compatibility
The Flash Network can be connected with other Layer 2 Networks, such as the Lightning Network and Raiden Network, known as “Bifrösts”. Channels opened from Flash nodes to Lightning nodes are filled with Bitcoin, whilst channels opened on the Flash side are filled with tBitcoin. This requires that the Layer 1 Bridge has already been used to emit tBitcoin on-chain. With this in place, all Bitcoin and tBitcoin are fully accounted for on both Networks and the Mjölnir can move value across the Bifröst instantly. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure1.png" width="400px" height="175px" />

Once tBitcoin is emitted into the Flash Network from the Bifröst, it can be used to trade across other tokens instantly. 
Further, the Flash Network can have a Bifröst with the Raiden Network. In this case, Bitcoin can be move in as tBitcoin across the Lightning Bifröst, traded into tEther across a Mjölnir liquidity node, and then emitted back across the Raiden Bifröst as Ether. 
With this in place; transactions can be made across all compatible chains - instantly. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure2.png" width="400px" height="175px" />

### Settlement
The security of the Flash Network’s mechanisms are not relevant unless we can ensure that Layer 2 mechanisms do eventually become settled on the root THORChain.

In general, users will be fully capable of doing this themselves and thus having control over their own security, but there are two main issues with this basic settlement system:
Users can be attacked by being censored or DDoSes on the network level to allow an attack to get through before that user is capable of settling on chain
Users have to stay online and available to ensure their security, which is not ideal from a UX perspective and will not work for attracting everyday end users that are not invested in the THORChain ecosystem
Improvements in these two areas is an ongoing research endeavor. A few mechanisms to be explored including having mechanisms for partial settlement to be possible on-chain, allowing users to commit their state on-chain periodically without needing to close channels as well as networks of watchers/bounty hunters that can be incentivized to provide settlement services to end users in a secure way. Some of this research has been partially explored by other projects, for example Celer [12].

## DEX Protocol

### Overview

The Flash Network is designed with the expectation that in order for a Layer 2 Network based DEX to succeed, it needs to fulfill the requirements of even the best centralized DEXs in order to gain user adoption and market share in addition to bringing some benefits of decentralization. The Flash Network aims to do so, being able to cater to all kinds of different traders. It is designed to support all of the following functional requirements with minimal compromises on decentralization:
- Instantaneous order placement for limit orders
- Instant cancellation of orders
- Instant order matching for limit orders (if available) and market orders
- Trustless and Decentralized Order Matching
- Trustless and Semi-Decentralized Order Book Aggregation, Order Relaying and Order Visibility
- Support for complex order types like IoC, FoK, AoN
- Support for OTC trading
- Cross Network Compatibility with other Networks (eg: Lightning, Raiden)
- High Liquidity and Availability of Orders
- Protection from frontrunning

### Related Work

The Flash Network draws on ideas from various other protocols and networks, and brings them together in a user-centric, customizable way that empowers users to trade with the methods that meet their requirements best. This section surveys some of the existing technologies and compares them in terms of the features and functions they support.
0x[7] is an Ethereum-based Protocol that allows creation and relaying of orders off-chain with on chain settlement. It supports instant limit order creation, but does not directly support market orders, automatic order matching, complex order types. It defers to other services to deal with relaying and liquidity.
Airswap[8] is a similar protocol more focused on peer-to-peer trades and OTC private trading. It defers to other services to matchmake peers for their trades, and does not have true order books like a typical exchange.
Liquidity.Network[9] is a protocol that allows nodes to join an off-chain 'Hub' that allows them to share liquidity and transact off chain with an alternative consensus mechanism run by hub operators, with trust mitigated by allowing users to make challenges and withdraw funds to the root blockchain in the event of a dispute. It is similar to Plasma[4], a general purpose Ethereum Layer 2 scaling solution. It compromises on decentralized availability/ease of use but not on decentralized security.

Idex[10] is a semi-centralized exchange that uses a similar off-chain construction to Plasma, but has more heavily centralized control over transactions. User funds are still protected and can be withdrawn in the event of a dispute, but their honest transactions are still at risk of being discarded after confirmed by the system. It compromises further on decentralization to favor ease of use.

Gnosis DutchX exchange[11] is an alternative decentralized exchange system that does not use orders and order books and rather allows token trades through rounds of discrete auctions. All previously mentioned protocols are susceptible to some kind of frontrunning, whether by root chain miners, relayers or sidechain operators, but the Gnosis DutchX exchange provides some protection against this. As a tradeoff, it becomes a very different kind of market without continuous trades or conventional UX.

### Layer 2 Trading
Layer 2 trading will facilitate instant trades “atomic swaps” between two parties. The key elements of this is the following:
Indication. A party must open a channel and put up an interest to sell an asset at a specified price. 
Offer. A second party must also open a channel with an interest to buy the asset.
Execution. The two assets must be atomically swapped at the terms agreed. 
This can be facilitated through the use of trading channels, a payment channel that indicates the order type amongst other trade information such as expiry. An aggregator then collects all trades across pairs, and a matcher performs the matching of trades. 
The following would be an example of a trading channel, posting a sell order of 100 T1 tokens for 1 T0 token (Rune) at a price of 0.01. The trading channel would need to be holding at least 100 TKN1 for this order to be fulfilled. 

```
{
    "type": "limit",
    "sending_amount": 100,
    "sending_token": “T1",
    "price": 0.01,
    "receiving_token": "T0"
}
```

A buyer would then create a trading channel with the following information:

```
{
    "type": "limit",
    "sending_amount": 1,
    "sending_token": “T0",
    “price": 100,
    "receiving_token": "T1"
}
```

These orders would then be matched and settled.

### Cohesive Design
DEX users will range from high end traders doing high frequency trading, arbitrage and algorithm/bot driven trading to mid level day traders using some scripts and help to passive traders using a web app and UI to big OTC traders. Trader requirements for trustlessness and decentralization also live along a spectrum, with traders being willing to compromise differently on the spectrums from trustlessness/decentralized to security and performance. By designing a DEX that can support users across these spectrums and meet their customized needs, along with sharing liquidity across all these different kinds of users, The Flash Network aims to become the ideal, most liquid exchange for any trader.

## Future research and solutions

### Liquidity Bonds
Adding matched liquidity to channels each time slows down trading by adding unnecessary on-chain transactions. Once a Hub is sufficiently collateralized, bonds can issued instead of real channel liquidity. This allows instant trading after channel formation. When the trader exits the channel the bond is redeemed from the existing hub liquidity. 

<img align="center" src="https://github.com/thorchain/Resources/blob/master/Whitepapers/Flash-Network/images/figure6.png" width="350px" height="400px" />

### Advanced Trade Types

The Flash Network can support advanced trade types such as Market & Limit Orders, IoC, FoK, AoN, and trade instructions such as Stop Loss & Take Profit. These are set in the order when a Trading Channel is opened. 
Limit Order, with trade pair and price:

```
{
    "type": "limit",
    "sending_amount": 100,
    "sending_token": “T1",
    "price": 0.01,
    "receiving_token": "T0"
}
```

Market Order, with trade pair. The order is matched with any open trades until filled. 

```
{
    "type": "market",
    "sending_amount": 100,
    "sending_token": “T1",
    "receiving_token": "T0"
}
```

IoC, with trade pair and price:
```
{
    "type": "IoC",
    "sending_amount": 100,
    "sending_token": “T1",
    "price": 0.01,
    "receiving_token": "T0"
}
```

FoK, with trade pair and price:
```
{
    "type": "FoK",
    "sending_amount": 100,
    "sending_token": “T1",
    "price": 0.01,
    "receiving_token": "T0"
}
```

AoN, with trade pair and price:

```
{
    "type": "AoN",
    "sending_amount": 100,
    "sending_token": “T1",
    "price": 0.01,
    "receiving_token": "T0"
}
```

The Flash Nodes continually scan and match trades, collecting fees. A Flash Explorer can generate visual order books of open buy and sell orders to create a familiar trading user experience. 

## Conclusion
THORChain is a lightning fast decentralised exchange with protocol-level trading features to achieve feature parity with the best centralised exchanges of the day; all with full self-sovereign asset management. THORChain solves the fundamental problems of existing decentralised exchanges with a fast on-chain trading experience, on-chain continuous liquidity and correct incentivisation economics for exchange and wallet developers.
THORChain matched with the Flash Network will allow for instant transactions across Layer 2 networks, allowing users to perform trades across assets instantly and trustlessly. The Flash Network has access to on-chain liquidity and price feeds and will be compatible with other Layer 2 networks to create a fast, multi-asset trading ecosystem. The Flash Network will be superior to other Layer 2 Networks due to having access to immediate on-chain liquidity, on-chain trustless price feeds and Liquidity Nodes are incentivised to hold collateral in channels they open.
THORChain is built for a new class of decentralised exchanges and transactional networks and will rapidly increase the useability of transacting with crypto-currency assets. 

## References
1. The Bitcoin Lightning Network: Scalable Off-Chain Instant Payments [Online]. Available at: https://lightning.network/lightning-network-paper.pdf
2. The Raiden Network [Online]. Available at: https://raiden.network/
3. Revive: Rebalancing O-Blockchain Payment Networks [Online]. Available at: https://eprint.iacr.org/2017/823.pdf
4. Plasma: Scalable Autonomous Smart Contracts [Online]. Available at: https://plasma.io/plasma.pdf
5. Plasma Cash: Plasma with much less per-user data checking [Online]. Available at: https://ethresear.ch/t/plasma-cash-plasma-with-much-less-per-user-data-checking/1298
6. NOCUST – A Non-Custodial 2nd-Layer Financial Intermediary [Online]. Available at: https://liquidity.network/NOCUST_Liquidity_Network_Paper.pdf
7. 0x: An open protocol for decentralized exchange on the Ethereum blockchain [Online]. Available at: https://0xproject.com/pdfs/0x_white_paper.pdf
8. Swap: A Peer-to-Peer Protocol for Trading Ethereum Tokens [Online]. Available at: https://swap.tech/whitepaper/
9. Liquidity Network: Blockchain Payments for Everyone [Online] Available at: https://liquidity.network/whitepaper_Liquidity_Network.pdf
10. IDEX: A Real-Time and High-Throughput Ethereum Smart Contract Exchange [Online] Available at: https://idex.market/static/IDEX-Whitepaper-V0.7.5.pdf
11. Gnosis DutchX [Online]. Available at: http://dutchx.readthedocs.io/en/latest/_downloads/DutchX_Documentation.pdf
12. Celer Network: Bring Internet Scale to Every Blockchain [Online]. Available at: https://www.celer.network/doc/CelerNetwork-Whitepaper.pdf
