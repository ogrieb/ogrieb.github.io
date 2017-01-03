---
layout: default
title: "Bitcoin Block Hashing Algorithm"
published: true
---

The [Bitcoin](https://bitcoin.org) [block hashing algorithm](https://en.bitcoin.it/wiki/Block_hashing_algorithm) is the core computation all bitcoin miners implement. Algorithmically, all miners attempt to solve a similar mathematical _problem_ concurrently. The problem given to all miners to solve is derived from the current state of the [bitcoin blockchain](https://en.bitcoin.it/wiki/Block_chain) as well as all [transactions](https://en.bitcoin.it/wiki/Transaction) published by bitcoin peers since the last block was accepted by the [network](https://en.bitcoin.it/wiki/Network) into the chain. Now, the first miner to find _and publish_ a peer-accepted solution for the current problem in the network basically shapes the newest [block](https://en.bitcoin.it/wiki/Block) appended to the chain.

The creation of a new block is called '[mining](https://en.bitcoin.it/wiki/Mining)' as with every new block the respective miner includes a transaction of a network agreed-upon amount of bitcoins 'from nowhere' to his own [address](https://en.bitcoin.it/wiki/Address). This will be his [reward](https://en.bitcoin.it/wiki/Mining#Reward) for putting in the [work](https://en.bitcoin.it/wiki/Proof_of_work).

Note, that although the problem is distributed to all miners in the network, _only_ the miner who successfully gets his solution published will get the reward. This makes mining each block a race - and the winner takes it all.

The _mathematical_ problem each miner attempts to solve is a [one-way function](https://en.wikipedia.org/wiki/One-way_function) with a well defined computational complexity. Finding a solution is hard, but verifying a published solution will be easy. The one-way function is defined as a double SHA-256 hash (cf. [Wikipedia article](https://en.wikipedia.org/wiki/SHA-2), [NIST specficiation](https://dx.doi.org/10.6028/NIST.FIPS.180-4)) over a data structure called a [_block header_](https://bitcoin.org/en/developer-reference#block-headers). This header is defined as an 80-byte string encoding various information about the current state of the bitcoin blockchain,



```
sha256(sha256(block_header))
```
