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

def blockhash(header: bytes) -> bytes:
    """Block hash over serialized header.

    Returns the double SHA-256 hash over the serialized block 'header', expected
    in little endian."""
    hash = sha256(header).digest()
    hash = sha256(hash).digest()
    return hash
```

We can test the above function against any header in the actual chain. The respective header elements we can get from any [block chain browser](https://en.bitcoin.it/wiki/Block_chain_browser), e.g. the [Blockexplorer](https://blockexplorer.com) website. The main site always shows the most recent block, right now, this is block [#447569](https://blockexplorer.com/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac).

![2016-12-28-bitexpblock447569.jpg]({{ site.url }}/assets/2016-12-28-bitexpblock447569.jpg)

This block can also be accessed in slightly more raw form by their [REST API](https://blockexplorer.com/api-ref), under the link <https://blockexplorer.com/api/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac>. The returned [JSON](https://en.wikipedia.org/wiki/JSON) struct contains a _lot_ of text, the list of transactions `"tx":["024b...6e51"]` has 693 entries and thus makes up most of the data presented. By shorting the `"tx"` array and structuring a bit, we get:

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

These values we need to serialize. A block [header](https://bitcoin.org/en/developer-reference#block-headers) is serialized in an 80 byte string, containing the concatenated entries:

- [version](https://bitcoin.org/en/developer-reference#block-versions) number
- [previous block header's hash](https://bitcoin.org/en/developer-reference#term-previous-block-header-hash)
- [merkle root hash](https://bitcoin.org/en/glossary/merkle-root)
- current [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time)
- [target](https://en.bitcoin.it/wiki/Target) [nBits](https://bitcoin.org/en/developer-reference#target-nbits)
- nonce, arbitrarily set by the miner

These values are presented in easily readable formats, i.e. mostly as base-10 or hex digits.

The raw bytes we want to compute with can better be accessed by their web [API](https://blockexplorer.com/api-ref). [Here](https://blockexplorer.com/api/block/00000000000000001e8d6829a8a21adc5d38d0a473b144b6765798e61f98bd1d) we can retrieve the very same block via their API. Just click on it, your browser will show the returned [JSON](https://en.wikipedia.org/wiki/JSON) struct as text. A _lot_ of text, the list of transactions `"tx":["024b...6e51"]` has 693 entries and thus makes up most of the data presented. When shorting the `"tx"` array and structuring the wall of text, we get:
