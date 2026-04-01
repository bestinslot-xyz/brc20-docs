# What is BRC2.0?

BRC2.0 is **EVM execution anchored to Bitcoin**, using Ordinals inscriptions as
the data and ordering layer, and designed to be **fully compatible with BRC-20
assets**.

- Developers write and deploy **standard Solidity smart contracts**.
- Users interact with those contracts by submitting **Bitcoin transactions**.
- Contract state is computed deterministically by replaying inscriptions through
  an EVM execution engine.

- Bitcoin provides transaction ordering and the fee market.
- The EVM provides programmability.

Everything else is intentionally minimized.

There are:

- No bridges
- No multisignature custodians
- No sequencers
- No validator networks
- No gas token
- No Layer 2 trust assumptions

For Ethereum developers, the mental model is straightforward:

- Execution semantics follow the EVM
- EVM bytecode and calldata are delivered via Bitcoin blocks and inscriptions
- Gas is prepaid through inscription size, not a native token

For builders accustomed to Layer 2 systems:

- Users do not need new wallets
- Assets are not wrapped or bridged
- Withdrawals do not depend on committees or challenge periods
- Failure modes are explicit and verifiable on Bitcoin

Why this matters:

- You get expressive smart contracts without departing from Bitcoin’s security
  model
- You reuse the existing Solidity and EVM tooling ecosystem (such as Foundry,
  Hardhat)
- You can build Bitcoin-native applications that interact directly with **BRC-20
  tokens and introspecting Bitcoin transactions.**
- Users only pay Bitcoin transaction fees—there are no additional protocol-level
  costs

Don’t think it’s “Ethereum on Bitcoin”. It is **Bitcoin as the base layer, with
the EVM as a programmable execution module**, designed to integrate cleanly with
existing developer workflows while avoiding new trust assumptions.
