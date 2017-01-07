---
layout: default
title: "On The Algorithmics of Bitcoin Mining - Part 1"
excerpt: "What algorithm do bitcoin miners implement?"
published: true
---

# On The Algorithmics of Bitcoin Mining - Part 1

## The Distributed Mining Service

From the [Bitcoin](https://bitcoin.org) [network's](https://en.bitcoin.it/wiki/Network) perspective, all miners provide a service for which they are sent a similar mathematical _problem_ to solve. This problem is derived from the current state of the network's [blockchain](https://en.bitcoin.it/wiki/Block_chain), as well as all [transactions](https://en.bitcoin.it/wiki/Transaction) published by peers since the last block was linked into the chain. Now, the first miner to find _and publish_ a peer-accepted solution for the current problem, basically, produces the newest block.

A [block](https://en.bitcoin.it/wiki/Block), considered as a data structure, is mainly a list of transactions which the network peers want to see performed. Besides those, a block must contain a structure called a '[block header](https://bitcoin.org/en/developer-reference#block-headers)'. For such a header to get accepted by the network, the resulting [block hash digest](https://en.bitcoin.it/wiki/Block_hashing_algorithm)  over it, considered as a big-endian number, must be lower than or equal to the current [target](https://en.bitcoin.it/wiki/Target). Finding such an acceptable block header for the current block is the problem [miners](https://en.bitcoin.it/wiki/Mining) attempt to solve.

## Competitive Mining

Miners don't solve the problem of finding the next acceptable block hash out of selfishness. As with every new block, the solving miner includes a [coinbase](https://en.bitcoin.it/wiki/Coinbase)-transaction of a network-wide, agreed-upon amount of bitcoins 'from nowhere' to his own [address](https://en.bitcoin.it/wiki/Address). This will be his [reward](https://en.bitcoin.it/wiki/Mining#Reward) for putting in the [work](https://en.bitcoin.it/wiki/Proof_of_work).

So, although, most of the block content to be included in the next block is distributed to all, every _individual_ miner will work to mine a slightly different block (header). Namely, they will all prepend their own coinbase transaction to the distributed list. This makes mining each block a competition to be the first to publish an acceptable block header, where the winner takes it all.

So, the most interesting question becomes: How big is one miner's _chance to win_ this competition?  
And right after that: How can a miner _increase_ his chances?

To answer these questions, we need to understand _how_ miners understand the problem, and further, how they go about solving it.

## The Actual Mining Problem

The actual _mathematical_ problem given to the miners is based on a [one-way function](https://en.wikipedia.org/wiki/One-way_function) with a well defined computational complexity. Hence, finding a solution is hard, but verifying a published solution will be easy. The one-way function is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4). The problem then stated by the network to all miners can be defined as:

```
Given the next block as currently agreed-upon by the network, find a correctly
structured block header, whose digest after computing its double hash will
comply with the target threshold.
```

```
sha256(sha256(block_header))
```
