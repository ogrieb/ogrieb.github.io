---
layout: default
title: "Bitcoin Block Hashing Algorithm"
published: true
---

The Bitcoin [block hashing algorithm](https://en.bitcoin.it/wiki/Block_hashing_algorithm) is the core computation any bitcoin miner implements.

This computation mainly involves a double SHA-256 hash (cf. [Wikipedia article](https://en.wikipedia.org/wiki/SHA-2), [NIST specficiation](https://dx.doi.org/10.6028/NIST.FIPS.180-4)) over a data structure called a _block header_:

```
sha256(sha256(block_header))
```
