---
layout: default
title: "Bitcoin Block Hashing"
excerpt: "Purpose and Python Example"
published: true
---

# Bitcoin Block Hashing

{% include figure.html img="2017-01-22-blockchain-overview.svg"
    cap="Figure 1. [_Simplified Bitcoin Block Chain_](https://bitcoin.org/en/developer-guide#block-chain) by unknown, licensed under the [MIT license](http://opensource.org/licenses/mit-license.php). _Developer Guide_, Bitcoin Project. Accessed 22 January 2017." %}

The block hash defines the chain links among the block chain, as every block contains a hash of the previous block. This is visualized in Figure 1. For more contextual information I recommend the wiki's [block chain article](https://en.bitcoin.it/wiki/Block_chain) and the developer guide's [block chain section](https://bitcoin.org/en/developer-guide#block-chain).

In this post, I want to take a look at the block hashing algorithm, its input, a serialized block header and hash byte orders.

## Input: Serialized Header

The [serialized header's](https://bitcoin.org/en/developer-reference#block-headers) 80 byte array is build up of 6 concatenated fields:

| Field | C Data Type | Format |
|-|-|
| [version](https://bitcoin.org/en/developer-reference#block-versions) number | long | little endian |
| [previous block header's hash](https://bitcoin.org/en/developer-reference#term-previous-block-header-hash) | char[32] | internal byte order |
| [merkle root hash](https://bitcoin.org/en/glossary/merkle-root) over transactions | char[32] | internal byte order |
| [Unix timestamp](https://en.wikipedia.org/wiki/Unix_time) | unsigned long |  little endian |
| [target](https://en.bitcoin.it/wiki/Target) [nBits](https://bitcoin.org/en/developer-reference#target-nbits) | unsigned long |  little endian |
| nonce | unsigned long |  little endian |

The `long` like data types are simple to serialize in [little endian](https://en.wikipedia.org/wiki/Endianness#Little-endian). Serialized hash character arrays in C, respectively byte arrays in Python, are not affected by endianness. For an explanation I refer to the section "When endianness affects code", in [this](https://www.ibm.com/developerworks/aix/library/au-endianc/) IBM developerWorks article. We still have to get the hash byte order right.

## Hash Byte Orders

Hashes in the bitcoin world are represented in two different [byte orders](https://bitcoin.org/en/developer-reference#hash-byte-order):

1. _Internal_ byte order: the 'normal' byte order in which any SHA-256 implementation will return its output is used with serialized blocks and headers. A [hashlib's](https://docs.python.org/3/library/hashlib.html) `sha256` object for example.
2. _RPC_ byte order: the _byte-wise reverse_ of the internal order is used by the Bitcoin Core’s [RPCs](https://bitcoin.org/en/developer-reference#remote-procedure-calls-rpcs) and [block chain browsers](https://en.bitcoin.it/wiki/Block_chain_browser).

In Python, conversion between the two orders can be realized by slicing with step width `-1`.

```py
inthash = blockhash(header)
rpchash = inthash[::-1]
```

## Actual Header Values from Block Explorer

A block chain browser like the [Block Explorer](https://blockexplorer.com) website is the easiest way to browse header elements from the actual block chain. Figure 2, below, shows the browser's page for the block at height 447569. Its 'BlockHash' for example is presented in _RPC_ byte order.

{% include figure.html img="2016-12-28-bitexpblock447569.jpg"
    cap="Figure 2. [_Block #447569_](https://blockexplorer.com/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac), 11 January 2017. _Website Screenshot_, Bitcoin Block Explorer. Accessed 11 January 2017." %}

The displayed header fields can also be retrieved in a more raw form, via the corresponding Block Explorer [API](https://blockexplorer.com/api-ref), with the block hash in RPC order as selecting `[:hash]` parameter.

```url
https://blockexplorer.com/api/block/[:hash]
```

The thus [returned](https://blockexplorer.com/api/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6acj) [JSON](https://en.wikipedia.org/wiki/JSON) struct contains a list of transactions with 693 entries and thus makes up most of the data. By shorting the `"tx"` array and structuring a bit, we get:

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

The `previousblockhash` and `merkleroot` hashes are hex strings in RPC byte order. `version`, `time` and `nonce` are given as integers. Whereas `bits` is returned as a big endian hex string.

The Python `bytes` object below is initialized with the very same values, but in the serialized block header format already. Note, that the hashes are in _internal_ byte order here.

```py
# Serialized block header at height 447569
serhead447569 = bytes.fromhex(
    "00000020\
    bb9d883e59fdb50bbe66bede526eb3741547a16467af1b000000000000000000\
    3947840edb1a5f4e7d5be5a6a125dbbdaaf7ea12e70320661d17168c93fc5ff1\
    017a7558\
    79430318\
    6053336d")
```

## Header Serialization

The Python function below can be used to serialize the above block header from the Block Explorer API values:

```py
from struct import pack, unpack

def serheadblockexp(
        version: int,
        prevblockhash: str,
        merkleroot: str,
        time: int,
        nbits: str,
        nonce: int) -> bytes:
    """Serialized block header from Block Explorer API Values

    Packs block header bytes from value types as returned from the Block
    Explorer API under https://blockexplorer.com/api/block/[:hash]"""
    return pack(
                "< l 32s 32s L L L",
                version,
                # hashes need to be converted from RPC to internal byte order
                bytes.fromhex(prevblockhash)[::-1],
                bytes.fromhex(merkleroot)[::-1],
                time,
                # nBits need to be unpacked from hex string into unsigned long
                unpack(">L", bytes.fromhex(nbits))[0],
                nonce)
```

## Block Hashing Algorithm

As of now, the developer guide and reference have no dedicated sections about the block hashing algorithm. Admittedly, the computation itself is rather straight forward and well-described in the [wiki](https://en.bitcoin.it/wiki/Block_hashing_algorithm). The computation is defined as a double [SHA-256](https://en.wikipedia.org/wiki/SHA-2) [hash](https://dx.doi.org/10.6028/NIST.FIPS.180-4) over the serialized block header:

```py
from hashlib import sha256

def blockhash(header: bytes) -> bytes:
    """Block hash over serialized block header"""
    hash = sha256(header).digest()
    hash = sha256(hash).digest()
    return hash
```

## Test

The following code tests the above functions for block #447569 against the known hash:

```py
# Test to recompute block hash #447569
rpcrefhash = bytes.fromhex(
    "0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac")
intrefhash = rpcrefhash[::-1]
print("Reference Hash:\n" + intrefhash.hex())

# parameters taken from
#  https://blockexplorer.com/api/block/0000000000000000025f4304cbcaa71ffe257eb14e5a12303d257bed95b9c6ac
header = serheadblockexp(
            536870912,
            "0000000000000000001baf6764a14715\
            74b36e52debe66be0bb5fd593e889dbb",
            "f15ffc938c16171d662003e712eaf7aa\
            bddb25a1a6e55b7d4e5f1adb0e844739",
            1484093953,
            "18034379",
            1832080224)
hash = blockhash(header)
print("Computed Hash:\n" + hash.hex())
assert (hash == intrefhash), "Wrong hash!"
```
