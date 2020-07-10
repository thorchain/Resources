# Orð Protocol

## A decentralised, secure messaging protocol for THORChain
devs@thorchain.org
V0.1 July 2018

### Abstract 
>Assymetric public key cryptography is used in conjunction with highly optimised KeyChains to allow users to share encryption keys. KeyChains are aggressively pruned lightweight sidechains that allow users to handshake public keys. Users handshake with a master Validator PublicKey, before posting their PubKey on the KeyChain. Validators look for, and match messaging partners, completing the handshake process. This ensures that only users and validators can decrypt public keys, preventing loss of privacy. Validators are permissioned and need to collude before they can maliciously scrape public keys. Various KeyChains will be instantiated each with different prune times; such as a 15 minute, 1 hour, 24 hours or 7 days. This dictates how long encrypted public keys are held on-chain, further increasing privacy. Users handshake keys and begin messaging, using their message partner's public address to direct messages to, and their partner's public key to encrypt. Messages are held only in the mem-pool and are removed once the user sends a read receipt. Validators broadcast the mem-pool and users "hone" in on an intermediary Validator to improve messaging latency. 

### Document Set
The following whitepapers should be read in conjunction:

- [THORChain](https://github.com/thorchain/Resources/tree/master/Whitepapers/THORChain) (this paper)
A lightning fast decentralised exchange protocol.

- [Bifröst Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Bifrost-Protocol)
Secure and fast cross-chain bridges for THORChain.

- [Flash Network](https://github.com/thorchain/Resources/tree/master/Whitepapers/Flash-Network)
A layer 2 Network for instant asset exchange on THORChain.

- [Yggdrasil Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Yggdrasil-Protocol)
Dynamic multi-set sharding for THORChain.

- [Æsir Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/AEsir-Protocol)
A self-amending forkless consensus algorithm for THORChain. 

- [Orð Protocol](https://github.com/thorchain/Resources/tree/master/Whitepapers/Ord-Protocol)
A decentralised, secure messaging protocol for THORChain.

## Overview

[Introduction](#introduction)	


[Conclusion](#conclusion)	


## Introduction
