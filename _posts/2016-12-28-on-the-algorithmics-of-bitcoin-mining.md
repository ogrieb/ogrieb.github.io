---
layout: default
title: "On The Algorithmics of Bitcoin Mining - Part 1"
excerpt: "What algorithm do bitcoin miners implement?"
published: true
---

# On The Algorithmics of Bitcoin Mining - Part 1

## The Distributed Mining Service

From the [Bitcoin](https://bitcoin.org) [network's](https://en.bitcoin.it/wiki/Network) perspective, all miners provide a service for which they are sent a similar computational _problem_ to solve. This problem is derived from the current state of the network's [blockchain](https://en.bitcoin.it/wiki/Block_chain) and all [transactions](https://en.bitcoin.it/wiki/Transaction) published by peers, since the last block was linked into the chain. Now, the first miner to find and publish a valid, i.e. peer-accepted, solution for the current problem produces the newest block.

A [block](https://en.bitcoin.it/wiki/Block), considered as a data structure, is mainly a list of transactions which the network peers want to see performed. Each block is preceded by a '[block header](https://bitcoin.org/en/developer-reference#block-headers)'. For such a header to get accepted by the network, the resulting [block hash digest](https://en.bitcoin.it/wiki/Block_hashing_algorithm)  over it, considered as a big-endian number, must be lower than or equal to the current [target](https://en.bitcoin.it/wiki/Target) number. Finding such an acceptable hash digest, respectively block header, for the current block is the problem [miners](https://en.bitcoin.it/wiki/Mining) attempt to solve.

## Competitive Mining

Miners don't solve the problem of finding the next block hash out of selfishness. As with every new block, the solving miner includes a [coinbase](https://en.bitcoin.it/wiki/Coinbase)-transaction of a network-wide, agreed-upon amount of bitcoins 'from nowhere' to his own [address](https://en.bitcoin.it/wiki/Address). This will be his [reward](https://en.bitcoin.it/wiki/Mining#Reward) for putting in the [work](https://en.bitcoin.it/wiki/Proof_of_work).

So, although, the list of peer-transactions to be included in the next block is distributed to all, each miner will prepend his own coinbase transaction to this list.  This makes mining each block a competition to be the first to publish an acceptable block header, and the winner takes it _all_.

## Serialized Block Header and Block Hash Computation

A [block](https://en.bitcoin.it/wiki/Block_hashing_algorithm) [header](https://bitcoin.org/en/developer-reference#block-headers) is serialized in an 80 byte octet string, containing the concatenated entries:

- [block version](https://bitcoin.org/en/developer-reference#block-versions) number, 4 byte
- [previous block header's hash](https://bitcoin.org/en/developer-reference#term-previous-block-header-hash), 32 byte
- [merkle root hash](https://bitcoin.org/en/glossary/merkle-root) over  [serialized transaction](https://bitcoin.org/en/glossary/serialized-transaction) list, 32 byte
- current [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time), 4 byte
- [target](https://en.bitcoin.it/wiki/Target) [nBits](https://bitcoin.org/en/developer-reference#target-nbits), 4 byte
- nonce, arbitrarily set by the miner, 4 byte

The corresponding block hash is simply defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4) over this string.

```
block_hash = sha256(sha256(block_header))
```


A block hash digest is not computed with a single hash function, but in several steps.

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
