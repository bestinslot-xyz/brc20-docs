---
title: "Bitcoin-Native Superpowers"
---

# Bitcoin-Native Superpowers

By leveraging Bitcoin as the base layer, smart contracts gain abilities that are
impossible or impractical on Ethereum or other EVM chains. These “superpowers”
let developers build Bitcoin-native dApps with deep protocol awareness.

## **Transaction Introspection**

Contracts can inspect Bitcoin transactions directly via precompiled contracts.
This allows you to:

- Verify that a user sent a specific UTXO or satoshis before executing logic
- Build token bonding curves pegged to Bitcoin flows
- Enforce custom asset entry conditions without relying on off-chain bridges

Effectively, contracts can reason about the **Bitcoin ledger itself**.

## **Satoshi Location**

Contracts can determine the exact location of a satoshi within a transaction
output. This enables:

- Precision tracking of scarce or ordinalized satoshis
- Conditional execution based on which satoshi or output a user controls
- Novel NFT-like use cases tied directly to individual satoshis

This level of granularity is **impossible on Ethereum**, where native assets are
fungible and abstracted.

## **Timelock Script Generation**

Contracts can generate Bitcoin timelock scripts programmatically:

- Enforce time- or height-based release of funds
- Build vaults, escrows, and delayed payouts without any external oracle
- Combine with on-chain contract logic for hybrid BTC+EVM workflows

These scripts are fully compatible with Bitcoin, meaning users can move funds in
and out without leaving the Bitcoin security model.

## **Examples of What Ethereum Cannot Do**

1. **Ordinalized Asset Control:** Track specific satoshis, not just fungible
   balances.
2. **BTC-Backed AMMs and Bonding Curves:** Accept and reason about raw Bitcoin
   inputs natively, without wrapping or trust intermediaries.
3. **Hybrid Contracts:** Combine EVM logic with Bitcoin timelocked ordinals,
   UTXO proofs, or ordinals in a single execution.
4. **Fully On-Chain Settlement:** Exit BRC20 tokens back to Bitcoin directly,
   with no bridge or multisig dependency.

In short: these superpowers let you **write contracts that are both EVM-native
and Bitcoin-native**, opening a class of applications that simply cannot exist
on Ethereum alone.
