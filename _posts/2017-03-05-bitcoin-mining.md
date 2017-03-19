---
layout: default
title: "Bitcoin Mining"
excerpt: "A technical introduction to the general bitcoin mining algorithm, with Python examples."
published: true
---

# Bitcoin Mining

In my last [post]({{ site.url }}/2017/01/29/bitcoin-blockhash) I wrote about the bitcoin block hash and its application with regards to the block chain, i.e. linking the blocks. In this post, I want to introduce bitcoin mining, respectively the corresponding mining algorithm which every miner runs in general.

{% include figure.html img="2017-03-19-mine-new-block.png"
    cap="Figure 1. Mining a new block." %}