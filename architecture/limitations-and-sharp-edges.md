# Limitations and Sharp Edges

While the system offers powerful Bitcoin-native smart contract capabilities, it
comes with important constraints and behavioral differences that developers must
understand.

## **Latency**

- Execution depends on Bitcoin block confirmation
- Transactions are not instantaneous, expect multiple-minute delays
- User-facing dApps should design for asynchronous flows and optimistic UI
  updates

## **No Synchronous Bitcoin State**

- Contracts cannot query Bitcoin state in real-time
- All operations are based on **indexed, confirmed inscriptions**
- Any assumptions of instant UTXO or balance visibility might lead to incorrect
  logic

## **Indexer Determinism Assumptions**

- Contract state is reconstructed by **deterministic replay of inscriptions**
- Indexers must process inscriptions in order and handle reorgs gracefully
- For developers, this means contracts should not assume simultaneous or
  out-of-order execution
- Conceptually similar to Ethereum replay tests, but the underlying chain is
  Bitcoin
