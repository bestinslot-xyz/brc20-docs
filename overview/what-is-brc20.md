---
title: "What is BRC-20?"
---

BRC-20 is a **metaprotocol** for fungible tokens on Bitcoin, leveraging the
Ordinals protocol to inscribe JSON data directly into Bitcoin transactions. A
metaprotocol is a set of rules and standards that exist as a layer above the
base blockchain protocol, Bitcoin nodes don't understand or enforce BRC-20
rules, but specialized indexers interpret inscribed data to maintain token state
and validate operations.

Unlike Ethereum's ERC-20, which uses on-chain smart contracts enforced by the
network itself, BRC-20 relies on social consensus: participants agree to follow
the same ruleset for interpreting inscriptions, with indexers providing the
canonical state of all tokens.

## How It Works

BRC-20 tokens are created through a simple three-operation model:

**Deploy:** Create a new token by inscribing deployment parameters (ticker, max
supply, mint limit)

**Mint:** Inscribe mint operations to claim tokens from the deployed supply

**Transfer:** Create transfer inscriptions, then send the inscribed satoshi to
move token

```json
{
  "p": "brc-20",
  "op": "deploy",
  "tick": "ordi",
  "max": "21000000",
  "lim": "1000"
}
```

Each operation is inscribed as JSON into a Bitcoin transaction, creating an
immutable record on-chain. Indexers scan these inscriptions in block order to
maintain the current state of all BRC-20 tokens—who owns what, total supply, and
transfer history.

## Key Characteristics

**Metaprotocol design:** BRC-20 is purely data inscriptions interpreted by
indexers following agreed-upon rules, not enforced by Bitcoin consensus

**First-come, first-served:** Token tickers are claimed on a first-inscription
basis; anyone can deploy any four-letter ticker

**UTXO-based:** Tokens exist within specific satoshis, making transfers require
careful UTXO management

**Indexer consensus:** Different indexers may diverge if they implement rules
differently, though major indexers converge on canonical interpretation

## Trading BRC-20 Today

BRC-20 tokens can be traded onchain through PSBT-based marketplaces like UniSat,
Best in Slot, Magic Eden, and OKX Web3. These platforms use Partially Signed
Bitcoin Transactions (PSBTs) to enable trustless atomic swaps, sellers sign
offers with specific SIGHASH flags that allow any buyer to complete the
transaction. The ecosystem has processed over 71,000 BTC in cumulative trading
volume (~$7 billion) and generated 6,922 BTC in miner fees since launch.
However, this architecture imposes significant limitations: automated market
makers are impossible without off-chain components, liquidity fragments across
isolated marketplaces with no cross-platform order aggregation, and
front-running

**Further reading:** [Ordinals Theory Handbook](https://docs.ordinals.com/) |
[BRC-20 Specification](https://layer1.gitbook.io/layer1-foundation/protocols/brc-20)
