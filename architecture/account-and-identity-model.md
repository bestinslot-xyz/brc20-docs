---
title: "Account and Identity Model"
---

# Account and Identity Model

The account model differs fundamentally from Ethereum and is important to
understand before writing any authorization logic.

## **No Private Keys for EVM Addresses**

EVM addresses in this system **do not have associated private keys**.

An EVM address is deterministically derived from a Bitcoin wallet’s output
script (pkscript) using:

`evm_address = keccak256(bitcoin_pkscript_bytes)[12:]`

This address exists solely as an execution identity inside the EVM.  
 It cannot sign messages and cannot produce Ethereum-style signatures.

As a result, any authentication scheme that assumes the existence of an ECDSA
key for an EVM address will not work.

## **Bitcoin Wallet as Identity**

A user’s **Bitcoin wallet is their identity**.

Ownership and intent are proven by signing messages using Bitcoin keys and
submitting those signatures alongside contract calls. The EVM address derived
from the wallet script serves as the canonical on-chain identifier for that
user.

Multiple Bitcoin addresses or scripts correspond to different EVM addresses,
even if controlled by the same wallet software.

## **BIP-322 Instead of ecrecover**

Ethereum’s ecrecover precompile cannot be used for authentication.

Instead, authentication can be performed using **BIP-322 message signatures**,
verified inside smart contracts via a dedicated precompiled contract.

The recommended flow is:

1. The user signs a message using their Bitcoin wallet (BIP-322).
2. The signature and signed message are passed as calldata.
3. The contract calls the BIP-322 verification precompile.
4. If verification succeeds, the derived EVM address is treated as the
   authenticated user.

Helper libraries are provided to standardize this pattern.

## **Interpreting msg.sender**

msg.sender represents the **derived EVM address of the Bitcoin script** that
initiated the operation.

It should be interpreted as:

“The Bitcoin script that authorized this execution,”

Not as an Ethereum-style externally owned account.

Because this address cannot sign, msg.sender should not be used as proof of
cryptographic intent by itself. Any action that requires explicit user consent
must additionally verify a BIP-322 signature.

## **Recommended Authorization Patterns**

### **Recommended**

- Verify BIP-322 signatures inside the contract and map them to derived EVM
  addresses
- Use explicit signed messages for sensitive actions (withdrawals, approvals,
  upgrades)
- Treat msg.sender as a stable identifier, not a signer
- Store permissions and balances against derived EVM addresses

### **Anti-Patterns**

- Using ecrecover or Ethereum-style signature assumptions
- Assuming msg.sender can produce signatures
- Relying on tx.origin
- Designing replayable actions without explicit nonce or domain separation

If contracts treat Bitcoin wallets as the root of identity and use BIP-322 for
intent verification, authorization remains explicit, verifiable, and compatible
with existing Bitcoin wallet infrastructure.
