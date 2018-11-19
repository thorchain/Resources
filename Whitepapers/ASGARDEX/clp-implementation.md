# CONTINUOUS LIQUIDITY POOLS

## Implementation specification for the continuous liquidity pools in THORChain
devs@thorchain.org

V0.1 September 2018

### Abstract 
>THORChain uses continuous liquidity pools to allow on-chain conversions of tokens into and out of Rune and a mathematical price. The continuous liquidity pools are permissionless; anyone can add or remove liquidity and anyone can use the pools to convert between assets. The pools rely on permissionless arbitrage to ensure correct market pricing of assets at any time. 


## Theory

Tokens on one side of the pool are bound to the tokens on the other. We can now determine the output given an input and pool depth.


