---
title: "Historical and Social Context"
---

# Historical and Social Context

## **The Ordinals Catalyst (2023)**

BRC-20 emerged in March 2023, just two months after Casey Rodarmor launched the
Ordinals protocol in January. Ordinals introduced a method to track and inscribe
data to individual satoshis, initially focused on creating NFT-like "digital
artifacts" on Bitcoin.

[@domodata](https://twitter.com/domodata) created the BRC-20 standard as an
experiment, a tongue-in-cheek nod to Ethereum's ERC-20. The first BRC-20 token,
ordi, was deployed on March 8, 2023, with a simple JSON structure anyone could
replicate. Within weeks, hundreds of tokens launched, and a speculative frenzy
began.

## **The Metaprotocol Debate**

BRC-20's metaprotocol design sparked fundamental questions about Bitcoin's
purpose:

**Critics** argued it was spam, clogging mempools with "useless" JSON
inscriptions and driving up transaction fees for regular Bitcoin users. Some
questioned whether metaprotocols that require off-chain indexers truly inherit
Bitcoin's security guarantees, or if they introduce new trust assumptions.

**Supporters** contended that anyone willing to pay fees has a legitimate claim
to block space—that Bitcoin is permissionless and should not discriminate
between transaction types. They argued that metaprotocols represent valid
experimentation on Bitcoin's most secure settlement layer, and that indexer
consensus is no different from how Bitcoin infrastructure has always worked
(exchanges, wallets, and explorers all run their own validation).

During peak BRC-20 activity in May 2023 and later surges, transaction fees
spiked dramatically, creating friction between traders and Bitcoin purists who
saw it as an attack on the network's intended use.

## **Market Dynamics and Ecosystem Growth**

The ordi token, initially minted for fractions of a cent, reached a market cap
exceeding $1 billion by late 2023, demonstrating real demand for Bitcoin-native
tokens despite technical limitations. This success spawned an ecosystem:

- **Marketplaces** like [UniSat](https://unisat.io/) and Best in Slot enabled
  trading inscribed satoshis
- **Indexers** competed to provide the canonical state of BRC-20 tokens, with
  services like Best in Slot becoming de facto standards
- **Wallets** added BRC-20 support, creating UX patterns for managing inscribed
  tokens
- **Alternative standards** emerged (ORC-20, Runes) as the community
  experimented with different metaprotocol designs

## **The Evolution to Programmability (2024-2025)**

Recognizing BRC-20's limitations—no smart contracts, limited composability,
purely manual operations—the community developed **BRC2.0** throughout 2024.
This extension adds an EVM-compatible execution layer while maintaining BRC-20's
inscription-based model.

BRC2.0's launch in 2025 represents a watershed moment: transitioning from simple
metaprotocol token ledgers to a programmable computing environment on Bitcoin.
The addition of smart contracts enables:

- Decentralized applications on Bitcoin
- DeFi primitives (DEXs, lending protocols, staking systems)
- Native Bitcoin operations via precompiles (transaction verification, signature
  validation)
- Complex financial logic previously impossible with BRC-20 alone

This evolution positions the BRC-20 ecosystem not just as a token standard, but
as a comprehensive application platform built on Bitcoin's security model.

## **Cultural Impact**

BRC-20's emergence validated that Bitcoin's block space has value beyond simple
value transfers. It demonstrated:

1. **Fee market dynamics**: Users will pay premium fees for functionality they
   value, regardless of Bitcoin maximalist opinions
2. **Permissionless innovation**: No one needed approval to experiment with a
   new metaprotocol—this is Bitcoin's core value proposition
3. **Bitcoin's flexibility**: The base layer, unchanged since before Ordinals,
   could support entirely new use cases through creative interpretation
4. **Metaprotocol viability**: Off-chain indexers with social consensus can
   enable complex systems while still leveraging Bitcoin's immutability

Whether viewed as innovation or spam, BRC-20 fundamentally altered how
developers think about building on Bitcoin. BRC2.0's introduction of
programmability takes this further—establishing Bitcoin not just as a settlement
layer for wrapped assets, but as a direct platform for application development
with native smart contract capabilities.

**Further
reading**:[Ordinals and BRC-20 History](https://trustmachines.co/learn/what-is-brc-20/)
|[Bitcoin Block Space Demand Analysis](https://insights.glassnode.com/bitcoin-ordinals-inscriptions/)
