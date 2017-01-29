---
layout: default
title: "The Bitcoin Block Hashing Algorithm"
excerpt: "Purpose and Python Example"
published: true
---

# The Bitcoin Block Hashing Algorithm

{% include figure.html img="2017-01-22-blockchain-overview.svg"
    cap="Figure 1. [_Simplified Bitcoin Block Chain_](https://bitcoin.org/en/developer-guide#block-chain) by unknown, licensed under the [MIT license](http://opensource.org/licenses/mit-license.php). _Developer Guide_, Bitcoin Project. Accessed 22 January 2017." %}

The block hash defines the chain links among the block chain, as every block contains a hash of the previous block. For more contextual information I recommend the wiki's [block chain article](https://en.bitcoin.it/wiki/Block_chain) and the developer guide's [block chain section](https://bitcoin.org/en/developer-guide#block-chain).

In this post, I just want to consider the block hashing _algorithm_ itself. As of now, the developer guide and reference have no dedicated sections about it. Admittedly, the computation is rather straight forward and well-described in the [wiki](https://en.bitcoin.it/wiki/Block_hashing_algorithm). The computation is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4) over an 80 byte input-string, the latter being the serialized block header. We can quickly prototype this in a Python function as:

```python
from hashlib import sha256

def blockhash(header):
    hash = sha256(header).digest()
    hash = sha256(hash).digest()
    return hash
```
