---
layout: default
title: "Bitcoin Block Hashing Algorithm"
published: true
---

The Bitcoin [block hashing algorithm][blockhash] is the core computation any bitcoin miner implements.

This computation mainly involves a double SHA-256 hash over a data structure called a _block header_:

```
sha256(sha256(block_header))
```

[blockhash]: https://en.bitcoin.it/wiki/Block_hashing_algorithm
