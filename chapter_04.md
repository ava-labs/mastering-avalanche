# Keys and Addresses

## Introduction

## Public Key Cryptography and Cryptocurrency

## Private Keys

## Public Keys

## Addresses

## Base58 Check

## Bech32

Addresses on the X-Chain and P-Chain use the Bech32 standard outlined in [BIP 0173](https://github.com/bitcoin/bips/blob/master/bip-0173.mediawiki). There are four parts to a Bech32 address scheme. In order of appearance:

* A human-readable part (HRP). On mainnet this is `avax`.
* The number `1`, which separates the HRP from the address and error correction code.
* A base-32 encoded string representing the 20 byte address.
* A 6-character base-32 encoded error correction code.

Additionally, an Avalanche address is prefixed with the alias of the chain it exists on, followed by a dash. For example, X-Chain addresses are prefixed with `X-`.

The following regular expression matches addresses on the X-Chain, P-Chain and C-Chain for mainnet, fuji and localnet. Note that all valid Avalanche addresses will match this regular expression, but some strings that are not valid Avalanche addresses may match this regular expression.

```zsh
^([XPC]|[a-km-zA-HJ-NP-Z1-9]{36,72})-[a-zA-Z]{1,83}1[qpzry9x8gf2tvdw0s3jn54khce6mua7l]{38}$
```
