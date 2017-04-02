---
layout: default
title: "Bitcoin Mining"
excerpt: "A technical introduction to the general bitcoin mining algorithm, with Python examples."
published: true
---

{% include mathjax.html %}

# Bitcoin Mining

In my last [post]({{ site.url }}/2017/01/29/bitcoin-blockhash) I wrote about the bitcoin block hash and its applications to the block chain, i.e. linking blocks. In this post, I want to introduce bitcoin mining, i.e., the general mining algorithm which every miner implements.

From the peer's perspective in the [bitcoin](https://bitcoin.org/en/developer-guide#p2p-network) [network](https://en.bitcoin.it/wiki/Network), miners provide a service to them when they want to perform a [transaction](https://bitcoin.org/en/developer-guide#transactions), i.e., transmit an amount of bitcoin from one peer to another. Technically, the miners help to include the respective transactions in the [transaction list](https://en.bitcoin.it/wiki/Transaction) of the next block, for a certain [fee](https://bitcoin.org/en/developer-guide#term-transaction-fee).

For the miners, this manifests as a computational problem which can be stated as:

> Given the block hash of the currently highest block $n$ in the chain, the merkle root $n+1$ defined over the new transactions to be included by the respective nodes $1..j$, then each of the miners $1..i$ attempts to find a new block header $n+1$ over which the corresponding block hash in _RPC_ byte order, considered as a big-endian number is lower than, or equal to the current [target threshold](https://bitcoin.org/en/developer-reference#target-nbits).


This procedure is visualized in Figure 1, below. Note, that this procedure is not a computation distributed among the miners, but a _competitive_ one. The first miner to find a solution for the current problem produces the newest block. Thus gets to keep all the transaction fees, as well as the [coinbase transaction](https://bitcoin.org/en/developer-reference#term-coinbase-tx).

{% include figure.html img="2017-03-19-mine-new-block.png"
    cap="Figure 1. Mining a new block." %}

I am not going to go into any more details in this post about the relationship between normal peers and miners, or the rewards for mining a block, respectively the motivation for miners. For more information about all these _why_ questions I would recommend to follow any of the given links, above. The focus of this post is instead going to be about the _what_, i.e., what do miners do to find a new block given the above problem statement.

## Fetching the Current Problem from the Network

Dedicated mining implementations do not implement the peer network interface themselves, e.g., to collect the peer's transactions or current target threshold. They can fetch this data via available interfaces respectively service providers:

- [Solo miners](https://bitcoin.org/en/developer-guide#solo-mining) can simply run a local instance of the [Bitcoin Core](https://bitcoin.org/en/bitcoin-core/) application to access its provided `getblocktemplate` [RPC](https://bitcoin.org/en/developer-guide#getblocktemplate-rpc) for all parameters they might need, e.g., the current transaction list to derive the Merkle root.
- [Pool miners](https://bitcoin.org/en/developer-guide#pool-mining) using fore exampoe the [Stratum protocol](https://bitcoin.org/en/developer-guide#stratum) would fetch all required parameters from the pool server's API.

The [Merkle root](https://bitcoin.org/en/developer-reference#merkle-trees) parameter is computed like a [common Merkle tree](https://en.wikipedia.org/wiki/Merkle_tree), respectively hash tree, over the coinbase and normal transaction hashes in the transaction list a miner wants to mine for. This parameter is not of so much interest to a dedicated mining implementation.

## The Target Threshold
