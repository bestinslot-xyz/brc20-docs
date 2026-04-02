---
title: "BRC-20"
---

**BRC20** is a token standard built on top of the **Bitcoin Ordinals Protocol**,
enabling the creation and trading of **fungible tokens** on the Bitcoin
blockchain by inscribing structured **JSON data** into satoshis using ordinal
inscriptions. The ordinal/inscription layer stores raw text on-chain, and
**off-chain indexers and wallets interpret** that text to derive token state
such as deployments, minting, and transfers.

## BRC20 JSON Format

Each BRC20 inscription must be valid **JSON** with the following fields
depending on the operation:

## **Common Fields (All Operations)**

- "p": "brc-20": Protocol identifier (must equal brc-20)
- "tick": Token **ticker symbol** (usually 3-4 uppercase characters)

## **1) Deploy**

Used to **deploy / define a new token**.

```json
{
  "p": "brc-20",
  "op": "deploy",
  "tick": "ordi",
  "max": "21000000",
  "lim": "1000"
}
```

- "op": "deploy": Operation type
- "max": Maximum token supply (stringified integer)
- "lim": Mint limit per inscription (optional)

## **2) Mint**

Used to mint tokens from a previously deployed token.

```json
{
  "p": "brc-20",
  "op": "mint",
  "tick": "ordi",
  "amt": "1000"
}
```

- "op": "mint": Mint operation
- "amt": Amount to mint (stringified integer)

## **3) Transfer**

Used to transfer tokens (must be inscribed and then sent to the recipient
address).

```json
{
  "p": "brc-20",
  "op": "transfer",
  "tick": "ordi",
  "amt": "500"
}
```

- "op": "transfer": Transfer operation
- "amt": Amount to transfer

Note: BRC20 inscriptions do not contain recipient addresses in the JSON. The
actual transfer is completed by inscribing a transfer and then sending that
ordinal to the recipient’s Taproot address.

## How It Works (At a Glance)

**Deploy:** The first valid deploy inscription for a ticker becomes the
canonical definition of that token (maximum supply, limits).

**Mint:** Subsequent mint inscriptions issue token units up to the defined cap.

**Transfer:** Transfer inscriptions attached to satoshis represent movement of
token balance when sent to another wallet.

The Bitcoin blockchain does not enforce BRC20 rules — validation is performed by
off-chain indexers and wallets that interpret the ordered inscriptions.

## Self mint

### Self issuance mechanism

The default brc-20 issuance model is permissionless: once a token is deployed,
anyone may mint until the supply cap is reached. This section introduces
**self-minting**, a restricted issuance mode where **only the deployer** is
allowed to mint tokens after deployment.

This is achieved via an explicit opt-in flag on the deploy inscription and
additional validation rules enforced by indexers.

### Deploy changes

A new optional field, `"self_mint"`, may be added to the deploy operation.

- `"self_mint": "true"` enables restricted issuance
- Any other value, or omission, defaults to public minting

When self-minting is enabled, **all mint inscriptions must use the deploy
inscription as their parent**. mint inscriptions without this parent
relationship are invalid.

Standard deploy (public mint):

```json
{
  "p": "brc-20",
  "op": "deploy",
  "tick": "token",
  "max": "21000000",
  "lim": "1000"
}
```

Self-mint deploy:

```json
{
  "p": "brc-20",
  "op": "deploy",
  "self_mint": "true",
  "tick": "token",
  "max": "21000000",
  "lim": "1000"
}
```

### Unlimited issuance via max=0

While legacy brc-20 rules disallow `max=0`, under self-mint semantics this value
is redefined to mean **unlimited supply**, bounded only by indexer max-uint64
constraints.

```json
{ "p": "brc-20", "op": "deploy", "self_mint": "true", "tick": "ordi", "max": "0", "lim": "1000" }
```

`max` still represents the global mint ceiling. brc-20 tokens cannot be
destroyed, and transfers to unspendable addresses do not reduce mint capacity.

### Namespace isolation

To avoid collisions with existing assets and legacy indexers, self-minted tokens
are isolated into **extended ticker namespaces** (5-byte and 6-byte tickers, not
4-byte tickers). indexers that do not support these extensions will simply
ignore them.

---

## 6-byte tickers with snipe protection

BRC-20 ticker namespace was extended to support **6-byte tickers** with Phase 1
launch of BRC2.0, while preserving compatibility with existing 4- and 5-byte
semantics. 6-byte tickers fully support **self-minting** and are intended for
programmable and composable use cases.

### Ticker constraints

6-byte tickers must:

- Be exactly 6 characters
- Match the regex: `^[A-Za-z0-9-]{6}$`
- Be treated case-insensitively

Invalid examples include unicode, symbols (other than `-`), or incorrect length.

4- and 5-byte tickers remain unchanged and unaffected.

### Self mint rules

6-byte tickers follow the same self-mint rules as 5-byte tickers:

- `"self_mint": "true"` restricts minting to the deployer
- Mint inscriptions must be children of the deploy inscription
- Absence of the flag implies public minting

### Snipe protection via pre-deploy

To prevent ticker sniping and front-running, 6-byte tickers require a
**pre-deploy commitment**.

Pre-deploy inscriptions commit to a ticker without revealing it by inscribing a
hash:

```json
hash = sha256(sha256(ticker_bytes + salt_bytes + deployer_pkscript))
```

This binds the ticker to both a salt and the deployer’s pkscript, preventing
replay or theft.

#### Pre-deploy inscription

```json
{
  "p": "brc-20",
  "op": "predeploy",
  "hash": ""
}
```

#### Deploy inscription

```json
{
  "p": "brc-20",
  "op": "deploy",
  "tick": "ticker",
  "salt": "",
  "self_mint": "true | false",
  "max": "21000000",
  "lim": "1000"
}
```

Deployment rules:

- Deploy inscription **must be a child** of the pre-deploy inscription
- Deploy must occur **at least 3 blocks after** the pre-deploy
- Indexers must reject deploys that violate ordering or hash validation
- Pre-deploy inscriptions do not expire and remain valid if transferred

> Note: pre-deploy inscriptions are accepted starting 10 blocks before the
> 6-byte namespace activation height (`912690`).

## **Required Tooling**

To create or interact with BRC20 inscriptions, users typically rely on:

- Ordinal inscription platforms (e.g., UniSat, Ordinals Wallet, LooksOrdinal)
- BRC20 explorers / indexers to derive balances and token state (e.g., Ordiscan,
  [BRC-20.io](http://BRC-20.io))

## **Limitations and Notes**

**Off-chain validation:** The protocol depends on indexers to determine the
canonical deploy, mint, and transfer history.

**Taproot addresses:** Transfers require Taproot-compatible addresses (bc1p...).
