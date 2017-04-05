---
layout: default
title: "Bitcoin Mining"
excerpt: "!!!Work in Progress!!! A technical introduction to the general bitcoin mining algorithm, with Python examples."
published: true
---

{% include mathjax.html %}

# Bitcoin Mining

In my last [post]({{ site.url }}/2017/01/29/bitcoin-blockhash) I wrote about the bitcoin block hash and its applications to the block chain, i.e. linking blocks. In this post, I want to introduce bitcoin mining, i.e., the general mining algorithm which every miner implements.

From the peers' perspective in the [bitcoin](https://bitcoin.org/en/developer-guide#p2p-network) [network](https://en.bitcoin.it/wiki/Network) miners provide a service to them when they want to perform a [transaction](https://bitcoin.org/en/developer-guide#transactions), i.e., transmit an amount of bitcoin from one peer/wallet to another. Technically, miners help the respective transactions to be included in the [transaction list](https://en.bitcoin.it/wiki/Transaction) of the next block. For this service they can expect a certain [fee](https://bitcoin.org/en/developer-guide#term-transaction-fee) from each transaction, as well as the network agreed on block finding reward, in form of the [coinbase transaction](https://bitcoin.org/en/developer-reference#term-coinbase-tx).

For the miners, providing this service manifests as a computational problem to be solved. This can be stated as:

- Each miner may define a transaction list including at least a coinbase transaction into his own wallet, as well as further, yet unprocessed, transactions published within the network.
- The miner who manages to find and present to the network a corresponding, i.e., network consensus compliant, _block header_ to this transaction list will have his list and header accepted by his network peers as the next block. In particular the header must:
    - Contain the Merkle root over his list
    - Contain the block hash of the currently highest block $n$ in the chain
    - Have a block hash digest, which in _RPC_ byte order, considered as a big-endian number is lower than, or equal to the current [target threshold](https://bitcoin.org/en/developer-reference#target-nbits)

This procedure is visualized in Figure 1, below. Note, that this procedure is not a cooperation among the miners, but a _competition_. The first miner to find a solution for the current problem defines the newest block. Thus, gets to keep the transaction fees and the coinbase transaction all to himself.

{% include figure.html img="2017-03-19-mine-new-block.png"
    cap="Figure 1. Mining a new block." %}

I am not going to go into any more details here about the relationship between normal peers and miners, or the rewards respectively motivation for miners. For more information about all such _why_ questions I recommend to follow any of the given links, above. The focus of this post is going to be about _what_ miners do to find a new block, given the above problem statement.

## Fetching the Current Problem from the Network

Dedicated mining implementations do not implement the peer network interface themselves, e.g., to collect the peer's transactions or current target threshold. They can fetch this data via available interfaces respectively service providers:

- [Solo miners](https://bitcoin.org/en/developer-guide#solo-mining) run a local instance of the [Bitcoin Core](https://bitcoin.org/en/bitcoin-core/) application to access its provided `getblocktemplate` [RPC](https://bitcoin.org/en/developer-guide#getblocktemplate-rpc) for all parameters they might need, e.g., the current transaction list to derive the Merkle root.
- [Pool miners](https://bitcoin.org/en/developer-guide#pool-mining) using for example the [Stratum protocol](https://bitcoin.org/en/developer-guide#stratum) fetch all required parameters from the pool server's API.

The [Merkle root](https://bitcoin.org/en/developer-reference#merkle-trees) parameter is computed like a [common Merkle tree](https://en.wikipedia.org/wiki/Merkle_tree), respectively hash tree, over the coinbase and normal transaction hashes in the transaction list a miner wants to mine for. This parameter is not of so much interest to a dedicated mining implementation.

## The Target Threshold
