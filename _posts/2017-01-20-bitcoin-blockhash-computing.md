---
layout: default
title: "Computing a Bitcoin Block Hash"
excerpt: "Recomputing the Bitcoin Block Hash #447569 in Python"
published: false
---

# Recomputing the Bitcoin Block Hash #447569

To get a better technical understanding of [bitcoin](https://bitcoin.org/en/developer-guide#mining) [mining](https://en.bitcoin.it/wiki/Mining) I started reading into the topics in the respective [developer guide](https://bitcoin.org/en/developer-guide), [developer reference](https://bitcoin.org/en/developer-reference) and [wiki](https://en.bitcoin.it/wiki). My main objective was to learn what algorithm [miners](https://en.bitcoin.it/wiki/Mining), in software or hardware, actually implement. I expected an explanation along three points:

1. input to mining implementation,
2. computations on input,
3. returned output.

The most interesting technical detail I found in the wiki article about the [block hashing algorithm](https://en.bitcoin.it/wiki/Block_hashing_algorithm). While it doesn't explain the full algorithm a miner implements very well (in my opinion), it does give a good explanation about their core operation: computing the block hash and comparing the resulting digest against a [target](https://en.bitcoin.it/wiki/Target).

## The Distributed Mining Service

From the [Bitcoin](https://bitcoin.org) [network's](https://en.bitcoin.it/wiki/Network) perspective, all miners provide a service for which they are sent a similar computational _problem_ to solve. This problem is derived from the current state of the network's [blockchain](https://en.bitcoin.it/wiki/Block_chain) and all [transactions](https://en.bitcoin.it/wiki/Transaction) published by peers, since the last block was linked into the chain. Now, the first miner to find and publish a valid, i.e. peer-accepted, solution for the current problem produces the newest block.

A [block](https://en.bitcoin.it/wiki/Block), considered as a data structure, is mainly a list of transactions which the network peers want to see performed. Each block is preceded by a '[block header](https://bitcoin.org/en/developer-reference#block-headers)'. For such a header to get accepted by the network, the resulting [block hash digest](https://en.bitcoin.it/wiki/Block_hashing_algorithm)  over it, considered as a big-endian number, must be lower than or equal to the current [target](https://en.bitcoin.it/wiki/Target) number. Finding such an acceptable hash digest, respectively block header, for the current block is the problem [miners](https://en.bitcoin.it/wiki/Mining) attempt to solve.

## Competitive Mining

Miners don't solve the problem of finding the next block hash out of selfishness. As with every new block, the solving miner includes a [coinbase](https://en.bitcoin.it/wiki/Coinbase)-transaction of a network-wide, agreed-upon amount of bitcoins 'from nowhere' to his own [address](https://en.bitcoin.it/wiki/Address). This will be his [reward](https://en.bitcoin.it/wiki/Mining#Reward) for putting in the [work](https://en.bitcoin.it/wiki/Proof_of_work).

So, although, the list of peer-transactions to be included in the next block is distributed to all, each miner will prepend his own coinbase transaction to this list.  This makes mining each block a competition to be the first to publish an acceptable block header, and the winner takes it _all_.



## The Actual Mining Problem

The actual _mathematical_ problem given to the miners is based on a [one-way function](https://en.wikipedia.org/wiki/One-way_function) with a well defined computational complexity. Hence, finding a solution is hard, but verifying a published solution will be easy. The one-way function is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4). The problem then stated by the network to all miners can be defined as:

```
Given the next block as currently agreed-upon by the network, find a correctly
structured block header, whose digest after computing its double hash will
comply with the target threshold.
```
