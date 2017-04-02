---
layout: default
title: "Bitcoin Mining"
excerpt: "A technical introduction to the general bitcoin mining algorithm, with Python examples."
published: true
mathjax_enabled: false
---

{% include mathjax.html %}

# Bitcoin Mining

In my last [post]({{ site.url }}/2017/01/29/bitcoin-blockhash) I wrote about the bitcoin block hash and its applications to the block chain, i.e. linking blocks. In this post, I want to introduce bitcoin mining, i.e., the general mining algorithm which every miner implements.

From the peer's perspective in the [bitcoin](https://bitcoin.org/en/developer-guide#p2p-network) [network](https://en.bitcoin.it/wiki/Network), miners provide a service to them when they want to perform a [transaction](https://bitcoin.org/en/developer-guide#transactions), i.e., transmit an amount of bitcoin from one peer to another. Technically, the miners help to include the respective transactions in the [transaction list](https://en.bitcoin.it/wiki/Transaction) of the next block, for a certain [fee](https://bitcoin.org/en/developer-guide#term-transaction-fee).

For the miners, this manifests as a computational problem which can be stated as:

> Given the block hash of the currently highest block $n$ in the chain, the merkle root $n+1$ defined over the new transactions to be included by the respective nodes $1..j$, then each of the miners $1..i$ attempts to find a new block header $n+1$ over which the corresponding block hash in _RPC_ byte order, considered as a big-endian number is lower than, or equal to the current [target threshold](https://bitcoin.org/en/developer-reference#target-nbits).


This is visualized in Figure 1 below.

{% include figure.html img="2017-03-19-mine-new-block.png"
    cap="Figure 1. Mining a new block." %}

This procedure is not a computation distributed among the miners, but a _competitive_ one. Meaning, the first miner to find a solution for the current problem produces the newest block, thus gets to keep all the transaction fees, as well as the [coinbase transaction](https://bitcoin.org/en/developer-reference#term-coinbase-tx).
