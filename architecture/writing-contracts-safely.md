# Writing Contracts Safely

Most Solidity contracts can be deployed and executed without modification.
However, some common Ethereum assumptions do not hold and should be addressed
explicitly.

## **Solidity Version and EVM Assumptions**

The execution environment follows standard EVM semantics and supports modern
Solidity versions.

You can assume:

- Deterministic EVM execution
- Standard storage, memory, and calldata behavior
- ABI encoding and decoding identical to Ethereum

You should not assume:

- Ethereum-style externally owned accounts
- A gas price market
- A synchronous transaction lifecycle

Contracts should be written as if execution is deterministic and replayable,
with all user intent explicitly provided as input.

## **What Breaks**

Certain patterns common in Ethereum contracts are incompatible or unsafe in this
environment.

- **ecrecover**EVM addresses do not have private keys (as they are generated
  using Bitcoin pkscripts). Any authentication logic relying on ecrecover will
  fail or be meaningless. Use BIP-322 verification via the provided precompile
  instead.
- **tx.origin Assumptions** There is no concept of a user-controlled EOA
  originating a call chain. Relying on tx.origin for authorization or security
  checks is unsafe.

## **Gas-Price-Based Logic**

There is no dynamic gas price, base fee, or priority fee. Contracts that rely on
gas price for:

- Anti-front-running logic
- Fee estimation
- Dynamic pricing

will not behave as intended. Execution limits are determined by inscription
size, not by runtime bidding.

## **What Works Well**

Several common contract patterns translate cleanly and predictably.

**ERC-20 / ERC-721 Logic -** Token standards, balances, allowances, and
transfers behave as expected. These patterns are well-suited for managing assets
within the execution environment.

**Bonding Curves and AMM-Style Math -** Purely deterministic pricing logic works
well, especially when combined with Bitcoin transaction introspection for
BTC-denominated flows.

**Vaults and Escrows -** Contracts that custody assets under explicit rules,
timelocks, or conditional releases are a strong fit, particularly when exits are
settled back to base BRC-20 balances.

**Bitcoin-Aware Contracts via Precompiles** Precompiled contracts enable direct
interaction with Bitcoin data, including:

- Transaction inspection
- Satoshi location tracking
- Timelock script generation

These capabilities allow contracts to enforce Bitcoin-native constraints that
are not possible on Ethereum.

**As a general rule:** If your contract logic is explicit, deterministic, and
does not rely on Ethereum-specific account or fee assumptions, it will behave
predictably and safely in this environment.
