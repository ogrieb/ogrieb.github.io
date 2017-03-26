---
layout: default
title: "Bitcoin Mining"
excerpt: "A technical introduction to the general bitcoin mining algorithm, with Python examples."
published: true
mathjax_enabled: true
---

# Bitcoin Mining

In my last [post]({{ site.url }}/2017/01/29/bitcoin-blockhash) I wrote about the bitcoin block hash and its applications to the block chain, i.e. linking blocks. In this post, I want to introduce bitcoin mining, i.e., the general mining algorithm which every miner implements.

From the perspective of the peers in the [bitcoin](https://bitcoin.org/en/developer-guide#p2p-network) [network](https://en.bitcoin.it/wiki/Network), miners provide a service to them. Those peers who want to perform a [transaction](https://bitcoin.org/en/developer-guide#transactions), i.e., transmit an amount of bitcoin from one peer to another, _technically_ want to have this transaction included in the transaction list of the next block. For the miners this manifests as a computational problem, which can be stated as:

bla \\( n+1 \\) blub

$$a^2 + b^2 = c^2$$

Given the block hash of the currently highest block _n_ in the chain, the merkle root _n+1_ defined over the new transactions to be included by the respective nodes _1..j_, then each of the miners _1..i_ attempts to find a new block header _n+1_ over which the corresponding block hash in _RPC_ byte order, considered as a big-endian number, is lower than or equal to the current [target threshold](https://bitcoin.org/en/developer-reference#target-nbits). This is visualized in Figure 1 below.

{% include figure.html img="2017-03-19-mine-new-block.png"
    cap="Figure 1. Mining a new block." %}

The first miner to find a solution for the current problem produces the newest block.
