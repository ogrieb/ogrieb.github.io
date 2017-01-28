---
layout: default
title: "Computing a Bitcoin Block Hash"
excerpt: "Recomputing the Bitcoin Block Hash #447569 in Python"
published: true
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

## Block Header Serializing and Block Hash Computation

A [block](https://en.bitcoin.it/wiki/Block_hashing_algorithm) [header](https://bitcoin.org/en/developer-reference#block-headers) is serialized in an 80 byte octet string, containing the concatenated entries:

- [block version](https://bitcoin.org/en/developer-reference#block-versions) number, 4 byte
- [previous block header's hash](https://bitcoin.org/en/developer-reference#term-previous-block-header-hash), 32 byte
- [merkle root hash](https://bitcoin.org/en/glossary/merkle-root) over  [serialized transaction](https://bitcoin.org/en/glossary/serialized-transaction) list, 32 byte
- current [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time), 4 byte
- [target](https://en.bitcoin.it/wiki/Target) [nBits](https://bitcoin.org/en/developer-reference#target-nbits), 4 byte
- nonce, arbitrarily set by the miner, 4 byte

The corresponding block hash is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4) over this string.

## Example

 Let's demonstrate the block hash computation by a real world example. [Block chain browsers](https://en.bitcoin.it/wiki/Block_chain_browser) like the [Blockexplorer](https://blockexplorer.com) website lets us browse the actual bitcoin blockchain. The main site always shows the most recent block, right now, at the time of this writing this is block [#447569](https://blockexplorer.com/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac).

![2016-12-28-bitexpblock447569.jpg]({{ site.url }}/assets/2016-12-28-bitexpblock447569.jpg)

 The values for this site are presented in an easily readable format, some abbreviated. The raw bytes we want to compute with can better be accessed by their web [API](https://blockexplorer.com/api-ref). [Here](https://blockexplorer.com/api/block/00000000000000001e8d6829a8a21adc5d38d0a473b144b6765798e61f98bd1d) we can retrieve the very same block via their API. Just click on it, your browser will show the returned [JSON](https://en.wikipedia.org/wiki/JSON) struct as text. A _lot_ of text, the list of transactions `"tx":["024b...6e51"]` has 693 entries and thus makes up most of the data presented. When shorting the `"tx"` array and structuring the wall of text, we get:

```json
{
    "hash":"0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac",
    "size":999125,
    "height":447569,
    "version":536870912,
    "merkleroot":"f15ffc938c16171d662003e712eaf7aabddb25a1a6e55b7d4e5f1adb0e844739",
    "tx":["024b...6e51"],
    "time":1484093953,
    "nonce":1832080224,
    "bits":"18034379",
    "difficulty":336899932795.80774,
    "chainwork":"00000000000000000000000000000000000000000037b812a69b538795d4f2e6",
    "confirmations":123,
    "previousblockhash":"0000000000000000001baf6764a1471574b36e52debe66be0bb5fd593e889dbb",
    "nextblockhash":"000000000000000000cda699b7856bac05591123eb6ad52896d50c5a22077128",
    "reward":12.5,
    "isMainChain":true,
    "poolInfo":{}
}
```

This gives us all of the header strings we are looking for, most of them as hex strings. All of these values are denoted in [big endian](https://en.wikipedia.org/wiki/Endianness#Big-endian). The endianness is easy to spot for block hashes as big endian numbers will have leading zeros, whereas little endian will have them trailing.

The struct holds some elements, which are not actually part of the header or block. These are some statistics the API provides. Right now we only want to compute the hash, so we can ignore everything which is not a header value.

- `hash` is actually the block hash we are trying to compute by ourselves. So this gives us a reference.
- `size` is the total block size in bytes.
- `height` is the height of the block in the chain, i.e. the .

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