---
layout: default
title: "On The Algorithmics of Bitcoin Mining"
published: true
---

# On The Algorithmics of Bitcoin Mining

From the [Bitcoin](https://bitcoin.org) [network's](https://en.bitcoin.it/wiki/Network) perspective, all miners are sent a similar mathematical _problem_ to solve. This problem is derived from the current state of the network's [blockchain](https://en.bitcoin.it/wiki/Block_chain), as well as all [transactions](https://en.bitcoin.it/wiki/Transaction) published by peers since the last block was linked into the chain. Now, the first miner to find _and publish_ a peer-accepted solution for the current problem, basically, produces the newest block.

A [block](https://en.bitcoin.it/wiki/Block), considered as a data structure, is mainly a list of transactions which the network peers want to see performed. Besides those, a block must contain a structure called a '[block header](https://bitcoin.org/en/developer-reference#block-headers)'. For such a header to get accepted by the network, the resulting [block hash digest](https://en.bitcoin.it/wiki/Block_hashing_algorithm)  over it, considered as a big-endian number, must be lower than or equal to the current [target](https://en.bitcoin.it/wiki/Target). Finding such an acceptable block header for the current block is the problem [miners](https://en.bitcoin.it/wiki/Mining) attempt to solve.

Now, miners don't solve this problem out of selfishness, as with every new block the respective miner includes a [coinbase](https://en.bitcoin.it/wiki/Coinbase)-transaction of a network-wide, agreed-upon amount of bitcoins 'from nowhere' to his own [address](https://en.bitcoin.it/wiki/Address). This will be his [reward](https://en.bitcoin.it/wiki/Mining#Reward) for putting in the [work](https://en.bitcoin.it/wiki/Proof_of_work).

Note, although the problem is distributed to all miners in the network, _only_ the miner who successfully gets his solution published will be rewarded. This makes mining each block a lottery, where the winner takes it all. In this lottery, however, each miner's winning chance is proportional to the _effective_ computing power he can muster, as we will see.

The actual _mathematical_ problem given to the miners is based on a [one-way function](https://en.wikipedia.org/wiki/One-way_function) with a well defined computational complexity. Hence, finding a solution is hard, but verifying a published solution will be easy. The one-way function is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4). The problem then stated by the network to all miners can be defined as:

```
Given the next block as currently agreed-upon by the network, find a correctly
structured block header, whose digest after computing its double hash will
comply with the target threshold.
```

```
sha256(sha256(block_header))
```
