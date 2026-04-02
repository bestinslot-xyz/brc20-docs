---
title: "Existing Documentation"
---

## Primary protocol specifications

The foundational BRC-20 documentation exists across two authoritative sources:
Domo's original specification and the Layer1 Foundation's updated protocol
documentation.

### Original BRC-20 whitepaper

- **URL:** [https://domo-2.gitbook.io/brc-20-experiment](https://domo-2.gitbook.io/brc-20-experiment)
- **Type:** Original protocol specification
- **Source:** Official/Primary (authored by [@domodata](https://x.com/domodata),
  BRC-20 creator)
- **Description:** The "definitive brc-20 white paper" containing the three core
  operations (deploy, mint, transfer), JSON inscription format specifications,
  balance state rules, and technical constraints including uint128 standard with
  max 18 decimals. Explicitly designated as unalterable.

### Layer1 Foundation protocol documentation

- **URL:**
  [https://layer1.gitbook.io/layer1-foundation/protocols/brc-20/documentation](https://layer1.gitbook.io/layer1-foundation/protocols/brc-20/documentation)
- **Type:** Updated protocol documentation
- **Source:** Official/Primary (Layer1 Foundation)
- **Description:** Current official documentation incorporating all protocol
  updates: block 816,000 ord version freeze (v0.90), Jubilee upgrade (ord 0.14),
  and block 837,090 additions including self-mint functionality, 5-byte tickers,
  and burn mechanism via OP_RETURN.

### Layer1 Foundation governance portal

- **URL:** [https://layer1.foundation/brc20](https://layer1.foundation/brc20)
- **Type:** Governance and organization portal
- **Source:** Official/Primary
- **Description:** Official non-profit organization page listing governance
  structure (President: Domo; Vice President: Isabel Foxen Duke), lead
  maintainers (Best in Slot, UniSat), and partner organizations. Links to
  official forum at [https://l1f.discourse.group/](https://l1f.discourse.group/).

### Protocol improvement proposals

- **URL:**
  [https://layer1.gitbook.io/layer1-foundation/protocols/brc-20/proposals](https://layer1.gitbook.io/layer1-foundation/protocols/brc-20/proposals)
- **Type:** Protocol improvement proposals
- **Source:** Official/Primary
- **Description:** Documents approved and pending proposals including Modular
  Complexity, Ord Version Freeze, Core Function Cleanup, and BRC20 IP1 (Issuance
  and Burn Enhancements).

## BRC2.0 technical documentation

BRC2.0 introduces EVM smart contract execution to Bitcoin's Layer 1 without
bridges or Layer 2 solutions. Gas costs are handled at the indexer level—users
pay only Bitcoin transaction fees.

### BRC2.0 programmable module repository

- **URL:** [https://github.com/bestinslot-xyz/brc20-programmable-module](https://github.com/bestinslot-xyz/brc20-programmable-module)
- **Type:** Core implementation repository
- **Source:** Official/Primary (Best in Slot)
- **Description:** Rust implementation using revm (Rust EVM) execution engine.
  Provides JSON-RPC 2.0 server supporting standard eth\_\* methods plus custom
  brc20\_\* methods.

### Layer1 Foundation BRC2.0 proposal

- **URL:**
  [https://l1f.discourse.group/t/brc2-0-programmable-module-proposal/766](https://l1f.discourse.group/t/brc2-0-programmable-module-proposal/766)
- **Type:** Protocol specification/technical whitepaper
- **Source:** Official/Primary (published March 31, 2025)
- **Description:** Complete architectural specification detailing EVM execution
  engine, address translation formula, smart contract deployment via
  inscriptions, and custom Bitcoin precompiles at addresses 0x...ff through
  0x...fb.

### BRC2.0 proposals repository

- **URL:** [https://github.com/bestinslot-xyz/brc20-proposals](https://github.com/bestinslot-xyz/brc20-proposals)
- **Type:** Protocol improvement proposals
- **Source:** Official/Primary
- **Description:** Formal BRC improvement proposals including:
  - 000: Programmable Module specification
  - 001: 6-byte ticker namespace
  - 002: BRC20 precompile removal
  - 003: EVM upgrade to Prague
  - 004: Bitcoin transaction ID precompile

### BRC2.0 explorer

- **Explorer URL** **(Mainnet):**
  [https://explorer.brc20.build](https://explorer.brc20.build/)
- **Explorer URL (Signet):** [https://explorer.bestinslot.xyz/signet](https://explorer.bestinslot.xyz/signet)
- **Type:** Development tools
- **Source:** Official
- **Description:** BRC2.0 chain with contract and transaction explorer.
  Activation height 230,000 on Signet, 923690 on Mainnet.

## Ordinals protocol documentation

BRC-20 operates as a layer built on Ordinals. Understanding the underlying
ordinal theory is essential for protocol development.

### Ordinal Theory Handbook

- **URL:** [https://docs.ordinals.com/](https://docs.ordinals.com/)
- **Type:** Official protocol documentation
- **Source:** Official/Primary (Casey Rodarmor, Raph Japh)
- **Description:** Definitive guide covering ordinal numbering scheme, five
  notation systems (Integer, Decimal, Degree, Percentile, Name), rarity
  classification, inscriptions documentation, and Runes specification. Available
  in 14 languages.

### ord GitHub repository

- **URL:** [https://github.com/ordinals/ord](https://github.com/ordinals/ord)
- **Type:** Reference implementation
- **Source:** Official/Primary
- **Stars:** 3,900+
- **Description:** Index, block explorer, and command-line wallet. Current
  release v0.24.2 (November 2025).

### Ordinals BIP (draft)

- **URL:** [https://github.com/ordinals/ord/blob/master/bip.mediawiki](https://github.com/ordinals/ord/blob/master/bip.mediawiki)
- **Type:** Technical specification
- **Source:** Official/Primary (Casey Rodarmor)
- **Description:** 15-line algorithm specification for ordinal assignment,
  Python pseudocode for subsidy calculation, satpoint notation, and
  compatibility analysis for covenants and Lightning Network.

### Casey Rodarmor's technical writings

- **URL:** [https://rodarmor.com/blog/ordinal-theory/](https://rodarmor.com/blog/ordinal-theory/)
- **Type:** Original technical introduction (July 21, 2022)
- **URL:** [https://rodarmor.com/blog/how-ordinals-came-to-be/](https://rodarmor.com/blog/how-ordinals-came-to-be/)
- **Type:** Technical history (September 5, 2024)
- **Source:** Official/Primary
- **Description:** First public introduction of ordinal theory and development
  timeline, including inspiration from Satoshi's "atoms" concept.

## GitHub repositories and implementations

### Best in Slot Open Protocol Indexer (OPI)

- **URL:** [https://github.com/bestinslot-xyz/OPI](https://github.com/bestinslot-xyz/OPI)
- **Type:** Reference indexer implementation
- **Source:** Official/Primary (Lead Maintainer)
- **Stars:** 220+
- **Description:** Open-source indexing client using fork of ord 0.23.2. Modules
  for BRC-20, Bitmap, SNS, and Runes. PostgreSQL backend with reorg protection
  and block hash verification.

### BRC-20 indexer rules documentation

- **URL:** [https://docs.bestinslot.xyz/brc-20-indexer-rules](https://docs.bestinslot.xyz/brc-20-indexer-rules)
- **Type:** Implementation specification
- **Source:** Official/Primary
- **Description:** Critical indexer rules: MIME type must be "text/plain" or
  "application/json"; ALL JSON fields must be strings (not numbers); use byte
  count for UTF-8 emoji tickers; reorg handling requirements.

### Additional repositories

|                       |                                                     |                          |
| --------------------- | --------------------------------------------------- | ------------------------ |
| **Repository**        | **URL**                                             | **Type**                 |
| BRC-20 Devs Org       | [https://github.com/brc20-devs](https://github.com/brc20-devs)                     | Development organization |
| UniSat BRC-20 Indexer | [https://github.com/unisat-wallet/libbrc20-indexer](https://github.com/unisat-wallet/libbrc20-indexer) | Go indexer library       |
| OKX BRC20 Indexer     | [https://github.com/okx/ord](https://github.com/okx/ord)                        | Protocol implementation  |
| Next-DAO Indexer      | [https://github.com/Next-DAO/brc20_indexer](https://github.com/Next-DAO/brc20_indexer)         | Node.js indexer          |

## Indexer API documentation

Four major indexers provide developer APIs for BRC-20 data access.

### Best in Slot API

- **URL:**
  [https://docs.bestinslot.xyz/reference/api-reference/ordinals-and-brc-20-and-runes-and-bitmap-v3-api-mainnet+testnet+signet/brc-20](https://docs.bestinslot.xyz/reference/api-reference/ordinals-and-brc-20-and-runes-and-bitmap-v3-api-mainnet+testnet+signet/brc-20)
- **Base URL:** [https://api.bestinslot.xyz/v3/](https://api.bestinslot.xyz/v3/)
- **Type:** REST API documentation
- **Source:** Official
- **Authentication:** API key via x-api-key header
- **Rate limits:** Free tier (10 calls/min, 1000/day), Grow ($99/mo), Scale
  ($699/mo)
- **Key endpoints:** /v3/brc20/wallet_balances, /v3/brc20/tickers,
  /v3/brc20/ticker_info, /v3/brc20/holders, enterprise historical data endpoints

### UniSat API

- **URL:** [https://docs.unisat.io/dev/open-api-documentation/api-for-bitcoin](https://docs.unisat.io/dev/open-api-documentation/api-for-bitcoin)
- **Base URL:** [https://open-api.unisat.io](https://open-api.unisat.io)
- **Swagger:** [https://open-api.unisat.io/swagger.html](https://open-api.unisat.io/swagger.html)
- **Type:** REST API documentation
- **Source:** Official
- **Authentication:** JWT Bearer token
- **Key endpoints:** `/v1/indexer/brc20/list`, `/v1/indexer/brc20/{ticker}/info`,
  `/v1/indexer/brc20/{ticker}/holders`, `/v2/inscribe/order/create/brc20-*`

### OKX API

- **URL:** [https://www.okx.com/web3/build/docs/waas/marketplace-ordinals-api](https://www.okx.com/web3/build/docs/waas/marketplace-ordinals-api)
- **Base URL:** [https://www.okx.com/api/v5/explorer/brc20/](https://www.okx.com/api/v5/explorer/brc20/)
- **Type:** REST API documentation
- **Source:** Official
- **Authentication:** OK-ACCESS-KEY + signature + timestamp + passphrase
- **Key endpoints:** /api/v5/explorer/brc20/token-list,
  /api/v5/explorer/brc20/token-details, /api/v5/mktplace/nft/ordinals/\*

### Hiro Ordinals API

- **URL:** [https://docs.hiro.so/en/apis/ordinals-api](https://docs.hiro.so/en/apis/ordinals-api)
- **Base URL:** [https://api.hiro.so/ordinals/v1/](https://api.hiro.so/ordinals/v1/)
- **Type:** REST API documentation
- **Source:** Official
- **Rate limits:** Free 500 RPM, higher limits via API key
- **Description:** Complete BRC-20 token data with caching optimization, powered
  by reorg-aware Bitcoin Indexer.

## Wallet SDK and integration documentation

### Sats Connect (Xverse)

- **URL:** [https://docs.xverse.app/sats-connect](https://docs.xverse.app/sats-connect)
- **npm:** sats-connect (~2M downloads)
- **Type:** Official SDK
- **Languages:** JavaScript/TypeScript
- **Description:** Primary library for Bitcoin wallet connections. Supports
  wallet_connect, signPsbt, ord_getInscriptions, createInscription. Compatible
  with Xverse, Leather, and other Bitcoin wallets.

### UniSat wallet integration

- **URL:** [https://docs.unisat.io/dev/unisat-developer-center](https://docs.unisat.io/dev/unisat-developer-center)
- **GitHub:** [https://github.com/unisat-wallet/unisat-dev-docs](https://github.com/unisat-wallet/unisat-dev-docs)
- **npm:** @unisat/wallet-utils
- **Type:** Official SDK
- **Description:** Browser extension API via window.unisat object. Methods
  include requestAccounts(), signMessage(), signPsbt(), sendInscription().

### Leather/Hiro Connect

- **URL:** [https://docs.hiro.so/stacks/connect/packages/connect](https://docs.hiro.so/stacks/connect/packages/connect)
- **npm:** @stacks/connect
- **Type:** Official SDK
- **Description:** RPC-based interface using request() method. Supports BTC,
  STX, Ordinals, and BRC-20 via Leather wallet.

### Core Bitcoin libraries

|                   |                                                   |                                                    |
| ----------------- | ------------------------------------------------- | -------------------------------------------------- |
| **Library**       | **URL**                                           | **Purpose**                                        |
| bitcoinjs-lib     | [https://github.com/bitcoinjs/bitcoinjs-lib](https://github.com/bitcoinjs/bitcoinjs-lib)      | Core Bitcoin transactions, PSBT (BIP-174), Taproot |
| micro-ordinals    | [https://github.com/paulmillr/micro-ordinals](https://github.com/paulmillr/micro-ordinals)     | Minimal inscriptions library                       |
| @scure/btc-signer | [https://www.npmjs.com/package/@scure/btc-signer](https://www.npmjs.com/package/@scure/btc-signer) | Audited PSBT/Taproot signing                       |
| msigner           | [https://github.com/me-foundation/msigner](https://github.com/me-foundation/msigner)        | PSBT signer for Ordinals marketplaces              |

## Relevant Bitcoin Improvement Proposals

BRC-20 and Ordinals depend on Taproot (activated block 709,632, November 2021)
and PSBT for marketplace transactions.

### Taproot BIPs (foundation for inscriptions)

|         |                    |                                                                  |                                        |
| ------- | ------------------ | ---------------------------------------------------------------- | -------------------------------------- |
| **BIP** | **Title**          | **URL**                                                          | **Relevance**                          |
| BIP-340 | Schnorr Signatures | [https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0340.mediawiki) | 64-byte signatures, batch verification |
| BIP-341 | Taproot            | [https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0341.mediawiki) | 4MB witness limit enables inscriptions |
| BIP-342 | Tapscript          | [https://github.com/bitcoin/bips/blob/master/bip-0342.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0342.mediawiki) | Script execution for Taproot           |

### PSBT BIPs (marketplace transactions)

|         |                     |                                                                  |                                       |
| ------- | ------------------- | ---------------------------------------------------------------- | ------------------------------------- |
| **BIP** | **Title**           | **URL**                                                          | **Relevance**                         |
| BIP-174 | PSBT Format         | [https://github.com/bitcoin/bips/blob/master/bip-0174.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0174.mediawiki) | Core format for BRC-20 atomic swaps   |
| BIP-370 | PSBT Version 2      | [https://github.com/bitcoin/bips/blob/master/bip-0370.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0370.mediawiki) | Enhanced transaction construction     |
| BIP-371 | Taproot PSBT Fields | [https://github.com/bitcoin/bips/blob/master/bip-0371.mediawiki](https://github.com/bitcoin/bips/blob/master/bip-0371.mediawiki) | Required for inscription transactions |

## Technical comparisons to other Bitcoin token standards

|                |                       |                          |               |                     |
| -------------- | --------------------- | ------------------------ | ------------- | ------------------- |
| **Protocol**   | **Data Storage**      | **State Validation**     | **Lightning** | **Smart Contracts** |
| BRC-20         | Witness (4MB)         | Off-chain indexers       | Limited       | None                |
| BRC2.0         | Witness + OP_RETURN   | Off-chain indexers + EVM | Limited       | Yes (full EVM)      |
| Runes          | OP_RETURN (<80 bytes) | UTXO-native              | Native        | None                |
| RGB            | Commitments only      | Client-side              | Native        | Yes (AluVM)         |
| Taproot Assets | Merkle roots          | Universe servers         | Native        | Limited             |

**Key architectural difference:** BRC-20 stores all data on-chain but token
balances depend entirely on correct indexer interpretation, introducing
centralization risks absent in UTXO-native protocols like Runes. Casey Rodarmor
designed Runes specifically to reduce UTXO proliferation caused by BRC-20.

### Security analysis resources

### CertiK risk analysis

- **URL:**
  [https://www.certik.com/resources/blog/ordinals-and-the-brc-20-standard-overview-and-risk-analysis](https://www.certik.com/resources/blog/ordinals-and-the-brc-20-standard-overview-and-risk-analysis)
- **Type:** Security audit/analysis
- **Source:** Community (reputable blockchain security firm)
- **Key findings:** BRC-20 tokens don't inherit Bitcoin's full security
  model—indexer operators can theoretically manipulate balances; indexer logic
  flaws could enable double-spending; low barriers enable fraudulent projects.

### Cyberscope audit services

- **URL:** [https://www.cyberscope.io/brc20-smart-contract-audit](https://www.cyberscope.io/brc20-smart-contract-audit)
- **Type:** Commercial audit service
- **Focus areas:** Indexer logic verification, off-chain database security,
  balance record integrity, JSON parsing vulnerabilities.
