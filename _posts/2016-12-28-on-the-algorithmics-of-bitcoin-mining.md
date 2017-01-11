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

## Block Header Serializing and Block Hash Computation

A [block](https://en.bitcoin.it/wiki/Block_hashing_algorithm) [header](https://bitcoin.org/en/developer-reference#block-headers) is serialized in an 80 byte octet string, containing the concatenated entries:

- [block version](https://bitcoin.org/en/developer-reference#block-versions) number, 4 byte
- [previous block header's hash](https://bitcoin.org/en/developer-reference#term-previous-block-header-hash), 32 byte
- [merkle root hash](https://bitcoin.org/en/glossary/merkle-root) over  [serialized transaction](https://bitcoin.org/en/glossary/serialized-transaction) list, 32 byte
- current [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time), 4 byte
- [target](https://en.bitcoin.it/wiki/Target) [nBits](https://bitcoin.org/en/developer-reference#target-nbits), 4 byte
- nonce, arbitrarily set by the miner, 4 byte

The corresponding block hash is simply defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4) over this string.

## Example

 Let us demonstrate the block hash computation by a real world example. [Block chain browsers](https://en.bitcoin.it/wiki/Block_chain_browser) like the [Blockexplorer](https://blockexplorer.com) website lets us browse the actual bitcoin blockchain. The main site always shows the most recent block, right now, this is block [#447569](https://blockexplorer.com/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac).

 The values for this site are presented in an easily readable format, some abbreviated. The raw bytes we want to compute with can better be accessed by their web [API](https://blockexplorer.com/api-ref). [Here is](https://blockexplorer.com/api/block/00000000000000001e8d6829a8a21adc5d38d0a473b144b6765798e61f98bd1d) the very same block, just click it, your browser will show the returned [JSON](https://en.wikipedia.org/wiki/JSON) struct as text. A _lot_ of text, the list of transactions `"tx":["024b...6e51"]` is quite huge and makes up most of the data presented. Structuring the wall of text a bit, and shorting the `"tx"` array, we get:

```json
{
    "pi": 3.141,
    "happy": true,
    "name": "Niels",
    "nothing": null,
    "answer": {
    "everything": 42
    },
    "list": [1, 0, 2],
    "object": {
    "currency": "USD",
    "value": 42.99
    }
}
```

This gives us most of the strings we are looking for, most of them as hex strings.

- The very first `hash` is actually the block hash we are trying to compute by ourselves.
- 'size' is the total block size in bytes.

The Python script below demonstrates this for the actual bitcoin block [125552](http://blockexplorer.com/block/00000000000000001e8d6829a8a21adc5d38d0a473b144b6765798e61f98bd1d).

```python
import hashlib

version = bytes.fromhex("01000000")
prevblockhash = bytes.fromhex("81cd02ab7e569e8bcd9317e2fe99f2de44d49ab2b8851ba4a308000000000000")
merkleroot = bytes.fromhex("e320b6c2fffc8d750423db8b1eb942ae710e951ed797f7affc8892b0f1fc122b")
time = bytes.fromhex("c7f5d74d")
nbits = bytes.fromhex("f2b9441a")
nonce = bytes.fromhex("42a14695")

header = version + prevblockhash + merkleroot + time + nbits + nonce
blockhash = hashlib.sha256(hashlib.sha256(header).digest()).digest()

print(blockhash)
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
