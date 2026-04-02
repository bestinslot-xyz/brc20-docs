---
title: "BRC20 Programmable Module"
---

BRC2.0 is a programmable execution layer built on top of BRC-20, adding
**EVM-compatible smart contracts to Bitcoin** through the same inscription-based
metaprotocol architecture. While BRC-20 provides token operations (deploy, mint,
transfer), BRC2.0 provides a full computing environment where Solidity contracts
can execute with direct access to Bitcoin's state through specialized
precompiles.

Like BRC-20, BRC2.0 is a **metaprotocol**, Bitcoin nodes don't execute smart
contracts, but specialized indexers run EVM execution engines that process
contract bytecode inscribed on Bitcoin. This enables developers to build DeFi
protocols, DAOs, and complex financial applications directly on Bitcoin without
wrapping assets or trusting bridge validators.

## How It Works

BRC2.0 extends BRC-20's inscription model with three new operation types that
enable smart contract deployment and interaction:

**Deploy:** Inscribe compiled Solidity contract bytecode to Bitcoin, with
indexers executing the constructor and assigning a Bitcoin-native address

**Call:** Invoke functions on deployed contracts, with indexers executing the
operation in their EVM environment and updating contract state

**Transact:** Combine BRC-20 token deposits with contract calls in a single
operation—the primary pattern for DeFi interactions

```json
{
  "p": "brc20-prog",
  "op": "call",
  "d": "0xA9059CBB..."
}
```

Each operation is inscribed on Bitcoin, creating an immutable audit trail.
Indexers execute EVM bytecode deterministically—all must produce identical
results through social consensus, just like BRC-20.

## Key Characteristics

**EVM compatibility:** Write standard Solidity contracts with familiar tooling
(Hardhat, Foundry, Remix)—minimal changes required

**Bitcoin-anchored execution:** Contract calls are inscribed on Bitcoin,
creating an immutable audit trail of all state transitions

**Precompile bridge:** Specialized precompiled contracts provide native access
to Bitcoin transactions, UTXO queries, and signature verification (BIP322)

**Deposit/Withdraw system:** Built-in bridge moves BRC-20 tokens between
Bitcoin-native state and smart contract state trustlessly

**Indexer consensus:** Like BRC-20, multiple indexers must agree on execution
results—divergence is resolved through community coordination

**Gas model:** Contract execution uses gas limits to prevent infinite loops,
separate from Bitcoin transaction fees for inscriptions

## What BRC2.0 Enables

BRC2.0 unlocks application categories previously impossible on Bitcoin:

**Decentralized Exchanges:** Constant product AMMs (Uniswap-style) with instant
swaps inside contracts—no PSBT coordination, no front-running via fee races, no
fragmented liquidity across marketplaces

**Lending Protocols:** Collateralized lending with algorithmic interest rates,
liquidations, and risk management—all executed deterministically by indexers

**Yield Farming:** Staking contracts that reward liquidity providers with
programmatic token emissions over time

**DAOs and Governance:** Token-weighted voting with on-chain proposal execution
and timelock mechanisms

**Options and Derivatives:** Complex financial instruments with automated
settlement based on oracle data or Bitcoin state

**NFT Marketplaces:** Programmable royalties, Dutch auctions, and
collection-wide operations

**The fundamental unlock:** composability. Contracts can call other contracts,
enabling complex DeFi primitives that build on each other—lending protocols can
integrate with DEXes for liquidations, yield farms can auto-compound through
swap contracts, and so on.

## Deposit and Withdraw Mechanics

BRC2.0 includes **a trustless bridge** between BRC-20 token state and contract
state:

**Depositing:** User inscribes a deposit operation → Indexers lock BRC-20
balance → Contract receives equivalent ERC-20-style balance internally

**Withdrawing:** Contract burns internal tokens and emits Withdraw event →
Indexers detect event → BRC-20 balance is credited back to user

This bridge has **no validators, no multisigs, no external dependencies**.
Indexers enforce the rules deterministically based on inscriptions and EVM
execution logs. Tokens can only be withdrawn if the contract properly burns
them, preventing unauthorized exits. The bridge is enforced by the same social
consensus that validates BRC-20 operations.

## Execution Model and State Finality

BRC2.0 execution follows this flow:

**Inscription:** User inscribes a call/transact operation on Bitcoin

**Confirmation:** Bitcoin block confirms (~10 minutes)

**Indexer execution:** Indexers process the inscription, execute EVM bytecode

**State update:** Contract storage updated, events emitted

**Consensus:** All indexers must produce identical post-state

State finality inherits Bitcoin's model: contract state changes are "final"
after the transaction is confirmed on Bitcoin, with an average block time of ~10
minutes. This is slower than Ethereum's 12-second finality but benefits from
Bitcoin's unmatched security and hashrate.

**Key difference from Ethereum:** BRC2.0 has no mempool for smart
contracts—users cannot see pending contract calls before they confirm. This
eliminates front-running at the contract level (though inscription-level MEV
still exists via Bitcoin fee races).

## Comparison to BRC-20

|                     |                           |                                                                |
| ------------------- | ------------------------- | -------------------------------------------------------------- |
| **Feature**         | **BRC-20**                | **BRC2.0**                                                     |
| **Operations**      | Deploy, Mint, Transfer    | Deploy contracts, Call functions, Transact                     |
| **Programmability** | None (fixed operations)   | Full Solidity support                                          |
| **Trading**         | PSBT marketplaces only    | AMMs, order books, auctions                                    |
| **DeFi**            | Not possible              | Lending, staking, derivatives                                  |
| **Execution**       | Indexer validates JSON    | Indexer runs EVM bytecode                                      |
| **State Model**     | Token balances only       | Contract storage + balances                                    |
| **Composability**   | Cannot combine operations | Contracts call other contracts                                 |
| **Speed**           | 10-min Bitcoin blocks     | 10-min Bitcoin blocks (but instant execution within contracts) |

BRC2.0 is a **superset** of BRC-20—all BRC-20 tokens can be deposited into
contracts, and withdrawn back to BRC-20 state. This enables gradual migration:
start with simple BRC-20 tokens, add programmability when needed without
redeploying or wrapping.

## EVM Compatibility and Differences

BRC2.0 aims for Solidity compatibility but has key differences from Ethereum:

**Supported:** Standard Solidity syntax, OpenZeppelin contracts, events and
logs, storage operations, familiar development tools

**Modified:** Block time is ~10 minutes (Bitcoin's interval), gas costs differ
due to indexer execution, addresses use Bitcoin format (bc1p...), no native ETH
equivalent

**Not supported:** Some time-dependent features work differently due to
10-minute blocks, certain Ethereum-specific opcodes may have limited support

The goal is maximum compatibility—most Solidity code runs unchanged, with
adjustments only for Bitcoin-specific features or timing assumptions.

## Why Developers Care

BRC2.0 solves BRC-20's fundamental limitations while maintaining Bitcoin's
security guarantees:

**No wrapped tokens:** Build with native BRC-20 assets directly, no bridge risk
or custodial solutions

**Familiar tooling:** Use Hardhat, Foundry, OpenZeppelin—existing Solidity
knowledge transfers completely

**Bitcoin security:** Smart contracts inherit Bitcoin's $800B+ security budget
and proven immutability

**Censorship resistance:** No admin keys, no protocol governance beyond social
consensus, no centralized control

**Composability:** Contracts interact with each other, enabling complex DeFi
primitives and application layers

**Instant execution:** Once inscribed, contract calls execute immediately—no
waiting for subsequent blocks for internal state changes

For developers coming from Ethereum, BRC2.0 offers Bitcoin's unmatched security
with Solidity's expressiveness. For Bitcoin maximalists, it proves Bitcoin can
support advanced applications without changing the base protocol—just creative
use of existing primitives, exactly like BRC-20 did for tokens.

**Launch status (2026):** BRC2.0 is launched with reference indexer
implementations, developer SDKs, and initial DeFi protocols. The ecosystem is
early but rapidly growing as builders recognize the opportunity to bring DeFi to
Bitcoin's trillion-dollar capital base without compromising on security or
decentralization.

Further reading: [BRC2.0 Specification](https://docs.brc20.build) |
[Solidity Documentation](https://docs.soliditylang.org/)
