---
layout: default
title: "Bitcoin Block Hashing Algorithm"
published: true
---

The [Bitcoin](https://bitcoin.org) [block hashing algorithm](https://en.bitcoin.it/wiki/Block_hashing_algorithm) is the core computation any [bitcoin miner](http://www.bitcoinmining.com) implements, as _efficient_ as possible.

This computation mainly involves a double SHA-256 hash (cf. [Wikipedia article](https://en.wikipedia.org/wiki/SHA-2), [NIST specficiation](https://dx.doi.org/10.6028/NIST.FIPS.180-4)) over a data structure called a [_block header_](https://bitcoin.org/en/developer-reference#block-headers).

```
sha256(sha256(block_header))
```
