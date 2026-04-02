---
title: "BiS AMM"
---

# **Technical Overview: AMM Sequencer Architecture**

This document provides a technical overview of the AMM sequencer architecture,
covering cryptographic primitives, settlement mechanisms, and security
assumptions.

---

## **1. Smart Wallets**

### **Non-Custodial Design**

Users hold their own BLS12-381 private keys. The sequencer never has access to
user funds or signing capabilities. All operations are signed client-side before
submission.

### **BIP322 Signature Verification**

All operations (deposits, swaps, liquidity operations, withdrawals) require a
BIP322 signature from the user's Bitcoin wallet as an additional security layer.
This is enforced by the sequencer to protect users from unauthorized operations.

The first operation for every wallet is a deposit, which establishes the binding
between the Bitcoin wallet and the BLS public key. The deposit signature
verifies ownership:

```text
Deposit Order:

Bitcoin Address:
BLS Public Key:
Token Address:

By signing this message, you authorize the creation of a deposit order with the above parameters.
```

### **Pubkey Index System**

To optimize on-chain storage, public keys are assigned a sequential index. This
allows operations to reference a compact identifier instead of the full 256-byte
BLS public key, significantly reducing transaction sizes and inscription costs.

### **Replay Protection**

Each wallet maintains a nonce that is atomically incremented with every
operation. The nonce is included in the signed message, preventing replay
attacks:

```js
message = keccak256(pubkey) || nonce || op_type || params_hash
```

### **Security Note**

**If a user loses access to their Bitcoin wallet, the associated smart wallet
cannot be recovered.** This is by design - the system is non-custodial, and
there is no recovery mechanism. Users must secure their private keys.

---

## **2. Bitcoin Batch Settlement**

### **Operation Queue**

User operations (swaps, liquidity adds/removes, withdrawals) are queued by the
sequencer, which monitors pending operations and aggregates them into batches.

### **Batching Triggers**

A new batch is created when either condition is met:

- **Operation threshold**: 500+ pending operations in the queue
- **Withdrawal priority**: Any pending withdrawal operations AND no currently
  pending batches

This ensures withdrawals are processed promptly while maintaining efficient
batching for regular operations.

### **Compression**

Batch data is compressed before inscription to minimize fees:

1. **Zstd compression** (level 22, maximum) is attempted first
2. **NADA encoding** is used as fallback if it produces smaller output
3. A 1-byte prefix indicates the encoding: `0x01` = NADA, `0x02` = Zstd

The envelope format for BRC-20:

```json
{ "p": "brc20-prog", "op": "t", "b": "" }
```

### **Sequential Execution**

Once the batch is inscribed and mined, the BRC-20 EVM executes operations
sequentially at the mined block height.

The system uses the **transact** command (signed EVM transactions) rather than
simple calls for two reasons:

1. **Key rotation resilience**: If the batch sender wallet key is compromised,
   operations can continue from a different wallet without interruption
2. **Nonce-based ordering**: The EVM transaction nonce enforces sequential
   execution on the BRC-20 side, preventing out-of-order processing

---

## **3. BLS Signatures**

### **Cryptographic Foundation**

The system uses **BLS12-381**, a pairing-friendly elliptic curve providing
128-bit security. This curve is widely used in blockchain systems (Ethereum 2.0,
Zcash) and enables efficient signature aggregation.

### **Short Signatures on G1**

Signatures are computed on the G1 curve (shorter representation), while public
keys reside on G2. This choice optimizes for aggregation efficiency since
signatures are aggregated more frequently than public keys.

Implementation uses `@noble/curves/bls12-381.js`:

```js
const P = bls12_381.G1.hashToCurve(msg_buf, { DST: 'BLS_SIG_BLS12381G1_XMD:SHA-256_SSWU_RO_NUL_' });
```

### **Message Format**

Each operation's message is constructed as:

```js
keccak256(pubkey) || nonce (4 bytes) || op_type (1 byte) || params_hash
```

For unwrap operations specifically:

```js
keccak256(pubkey) || nonce || 0x06 || keccak256(pkscript) || amount || fee
```

### **Signature Aggregation**

Individual signatures from multiple operations are aggregated into a single
signature per batch:

```js
const aggregated_sigs = bls12_381.shortSignatures.aggregateSignatures(signatures); const neg_aggregated_sigs = aggregated_sigs.negate();
```

The negated aggregated signature is submitted to the sequencer contract's
`processBatch()` function for on-chain verification.

### **Security Benefit**

**N signatures compress to 1 aggregated signature**, providing constant
verification cost regardless of batch size. This dramatically reduces on-chain
verification costs and inscription sizes.

---

## **4. BTC-frBTC Wrapping**

### **Wrap (BTC to frBTC)**

1. User constructs a `wrapAndExecute2()` call inscription
2. BTC is sent to the handler wallet in the send-to-OP_RETURN transaction
3. The sequencer validates:
   - Transaction structure and signatures
   - Fee rate within acceptable range
   - Mempool acceptance via `testmempoolaccept`

4. Upon inscription confirmation, frBTC is minted to the user's balance

The wrap data includes:

- Token address
- Ticker index
- User pubkey and pubkey index
- BLS signature for emergency withdrawal
- EC signature for index verification

### **Unwrap (frBTC to BTC)**

1. User submits a BLS-signed unwrap request containing:
   - Public key and nonce
   - Output pkscript (destination)
   - Amount and BTC fee

2. Signature is verified against the message:

   ```js
   keccak256(pubkey) || nonce || 0x06 || keccak256(pkscript) || amount || fee
   ```

3. Operation is queued and processed in the next batch
4. BTC output is created to the specified pkscript

Minimum unwrap amount: 1000 satoshis

### **Custody Model**

The BTC handler wallet is secured by a **multisig arrangement** operated by
Subfrost.

|                  |                       |                               |
| ---------------- | --------------------- | ----------------------------- |
|                  |                       | AspectCurrent StatePlanned    |
| **Custody Type** | Multisig              | Committee-based               |
| **Signers**      | Subfrost team members | Independent committee members |

**Roadmap**: The custody model will transition from internal Subfrost signers to
an independent committee approach, distributing trust across multiple parties
external to the core team.

---

## **5. Security & Trust Assumptions**

### **Trust Model**

```text
User --> [signs with BLS key] --> Sequencer --> [batches & inscribes] --> Bitcoin --> [BRC-20 executes] (non-custodial) (liveness) (finality)
```

|                |                                                                                    |
| -------------- | ---------------------------------------------------------------------------------- |
|                | ComponentTrust Assumption                                                          |
| **User**       | Holds private keys, signs all operations                                           |
| **Sequencer**  | Trusted for **liveness only**, not custody. Cannot forge signatures or steal funds |
| **Bitcoin**    | Provides finality and data availability                                            |
| **BRC-20 EVM** | Executes state transitions deterministically                                       |

**Roadmap - Force Exit Mechanism**: A force exit mechanism is planned to
eliminate sequencer liveness trust. This will allow users to withdraw funds
directly on-chain if the sequencer becomes unresponsive, ensuring users are
never locked out of their assets.

### **Bitcoin Finality & Reorg Handling**

The system monitors block hashes on both Bitcoin and BRC-20 chains. On reorg
detection:

1. **Detection**: Block hash mismatch triggers reorg handler
2. **Wait for sync**: System waits for BRC-20 to process the reorg
3. **Find divergence**: Locate the last confirmed (correct) block height
4. **Rollback state**: Clear executed batches and operations after divergence
   point
5. **Preserve snapshots**: Verified historical balances/pairs up to the last
   correct block are retained
6. **Reprocess**: Re-execute all blocks to rebuild current state

### **Emergency Stop**

A global halt mechanism (`emergency_stop` flag) is triggered when:

- Unexpected operation failures occur
- State mismatches are detected between local computation and on-chain logs
- Event names or success status don't match expected values

When emergency stop is active, all new operations are rejected with HTTP 503.

### **Replay Protection System**

Per-wallet nonce is verified and incremented atomically for every operation. The
nonce is included in every signed message, preventing replay attacks and
ensuring sequential processing.

### **Fee Validation**

Fee rates are validated against [mempool.space](https://mempool.space) data with
a tolerance of ±1-5 sat/vB. Transactions with fee rates outside acceptable range
are rejected to prevent overpaying or stuck transactions.

### **On-Chain Verification**

The `processBatch()` function on the sequencer contract verifies the aggregated
BLS signature before executing any state changes. This ensures that even if the
sequencer is compromised, it cannot execute unauthorized operations.

### **Transaction Ordering**

Operations are processed in **strict FIFO (First In, First Out) order**. The
sequencer cannot reorder transactions within a batch, eliminating the
possibility of sequencer-initiated front-running or MEV extraction.

---

## **6. Audits**

The smart contracts have been audited by Hashlock, an independent security firm.

|          |                                                  |               |        |                                                                                                                                 |
| -------- | ------------------------------------------------ | ------------- | ------ | ------------------------------------------------------------------------------------------------------------------------------- |
|          |                                                  |               |        | AuditorScopeDateRatingReport                                                                                                    |
| Hashlock | BiS_Swap, bls12lib.sol & Uniswap Smart Contracts | November 2025 | Secure | [Full Report](https://hashlock.com/wp-content/uploads/2025/09/Best-in-Slot-2nd-Smart-Contract-Audit-Report-Final-Report-v1.pdf) |

For additional audit information, visit:
[hashlock.com/audits/best-in-slot](https://hashlock.com/audits/best-in-slot)

---

## **References**

- **BLS12-381**:
  [IETF Draft - BLS Signatures](https://datatracker.ietf.org/doc/html/draft-irtf-cfrg-bls-signature)
- **BIP322**:
  [Generic Signed Message Format](https://github.com/bitcoin/bips/blob/master/bip-0322.mediawiki)
- **Noble Curves**: [@noble/curves](https://github.com/paulmillr/noble-curves)
- **BRC-20**:
  [BRC-20 Token Standard](https://domo-2.gitbook.io/brc-20-experiment/)
