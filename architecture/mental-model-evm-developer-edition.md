# Mental Model: EVM Developer Edition

The simplest way to think about this system is:

- **Ethereum execution, but blocks are Bitcoin blocks.**

Smart contracts run in an EVM and follow familiar Ethereum semantics. The
difference is how transactions enter the system and how execution is paid for.

## **Blocks**

Instead of Ethereum blocks, **Bitcoin blocks define ordering**.

Operations are processed in the order their inscriptions appear on Bitcoin.
There is no separate block production, proposer role, or mempool for the EVM
layer.

If a Bitcoin transaction is reordered or reorged, the corresponding EVM
execution is replayed accordingly.

## **Transactions**

There is no EVM mempool.

What would normally be an Ethereum transaction is represented as an **Ordinals
inscription**. Deployments, contract calls, and signed EVM transactions are all
submitted as inscription data and activated by sending them to the module
address.

From a contract’s perspective, execution is identical to a normal EVM call. From
a user’s perspective, interaction happens entirely through Bitcoin transactions.

## **Gas**

There is no gas token and no dynamic gas price.

Execution limits are derived from **inscription size**:

- Each inscribed byte grants a fixed amount of gas
- Larger inscriptions allow more computation
- Gas is prepaid by paying higher Bitcoin fees for larger transactions

This means:

- You never reason about gas markets or base fees
- There is no priority fee or bidding mechanism
- The cost of computation is directly tied to Bitcoin’s fee market

## **What Feels the Same as Ethereum**

- Solidity smart contracts
- EVM execution semantics
- Contract storage and state persistence
- ABI encoding and calldata
- Events and logs
- ERC-style token logic

Most existing Solidity code works unchanged, provided it does not rely on
Ethereum-specific assumptions.

## **What Is Different**

- Accounts do not have private keys; identity is derived from Bitcoin scripts
- ecrecover is not usable for authentication, and BIP-322 can be used instead
- There is no notion of gas price or gas refunds
- Transactions are not instantaneous and follow Bitcoin block times
- Execution is triggered by inscriptions, not by an EVM mempool

If you treat Bitcoin as the settlement and ordering layer, and the EVM as a
deterministic execution module layered on top, the system behaves predictably
and avoids the trust tradeoffs common in rollups and Layer 2 designs.
