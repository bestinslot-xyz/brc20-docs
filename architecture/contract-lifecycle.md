---
title: "Contract Lifecycle"
---

Smart contracts follow a familiar lifecycle, with differences primarily in how
actions are submitted and finalized.

## **Deploy**

A contract is deployed by inscribing its compiled EVM bytecode and submitting
that inscription to the Programmable Module.

The deployment:

- Uses standard EVM creation semantics
- Is executed deterministically when indexed
- Produces a contract address in the same way as Ethereum

Once deployed, the contract becomes part of the global execution state and can
be called by any subsequent operation.

There is no deployment transaction in the Ethereum sense; deployment is
triggered by a Bitcoin transaction that carries the inscription data.

## **Call**

Contracts are invoked by inscribing calldata and submitting it to the module.

From the contract’s perspective:

- The call behaves like a normal EVM transaction
- Calldata is ABI-encoded
- Execution either succeeds or reverts

Calls may be authenticated using either:

- Bitcoin-native identity (via BIP-322), or
- Signed EVM transactions (transact), if explicitly used

Multiple contracts can be composed and called within a single execution, subject
to gas limits.

## **Persist State**

Contract state is persisted exactly as in Ethereum:

- Storage writes are committed on successful execution
- Reverted calls do not modify state
- State transitions are deterministic and replayable

State is not stored on Bitcoin directly. Instead, it is reconstructed by
replaying all valid inscriptions in order.

As long as indexers process the same Bitcoin history, the contract state is
identical.

## **Emit Logs**

Contracts may emit standard EVM events and logs.

These logs:

- Follow Ethereum’s event model
- Can be indexed by off-chain services and frontends
- Are deterministic and replayable

Logs are not written to Bitcoin, but are derived from execution and can be
reproduced by any compliant indexer.

## **Withdraw Back to Base BRC-20**

Contracts may release assets back to base BRC-20 balances via withdrawals.

Conceptually:

- Assets are locked under contract control during execution
- A withdrawal reduces the contract’s balance
- The corresponding BRC-20 balance becomes spendable on Bitcoin again

Withdrawals are finalized through Bitcoin transactions and do not rely on
bridges or custodians.

From the developer’s perspective, this behaves like exiting from a smart
contract back to the base asset layer, with Bitcoin providing final settlement.
