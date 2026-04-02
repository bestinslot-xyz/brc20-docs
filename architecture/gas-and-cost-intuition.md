---
title: "Gas and Cost Intuition"
---

# Gas and Cost Intuition

While there is no native gas token, execution is **still limited and metered**.
Understanding how gas works in this environment is critical for writing
efficient contracts and predicting costs.

## **Inscription Size ↔ Maximum Execution**

Each inscription on Bitcoin pre-pays execution resources:

- Gas is **directly proportional to the size of the inscription**
- Larger inscriptions allow more computation and state changes
- Each byte of an inscription grants a fixed gas allowance

Think of it as “your transaction byte size is your gas budget.” Unlike Ethereum,
there’s no dynamic pricing—**execution cost is baked into the transaction size
and Bitcoin fee**.

## **How to Think About Optimization**

- Smaller calldata and bytecode = lower fees, faster indexing, and fewer block
  constraints
- Reuse logic and precompiled contracts where possible to reduce instruction
  count
- Compress calldata using NADA or ZSTD to minimise fees where it matters

Optimization is about **packing more computation into fewer bytes**, rather than
chasing gas prices.

## **Why Padding Exists and When to Use It**

- JSON padding allows developers to **increase the gas limit artificially** for
  an operation without changing its logic
- Useful when an operation requires more computation than the current calldata
  size allows
- Must be done responsibly: padding increases Bitcoin fees and should only be
  used to cover legitimate computational needs

## **Rough Cost Envelopes (Not Promises)**

Costs scale with:

1. **Byte size of the inscription**
2. **Bitcoin network fee rate**

As a guideline:

- Small calls (~100 bytes) are inexpensive
- Large deployments (~10–100 KB) can be significant
- Compression often reduces fees by 3–5x depending on calldata patterns

Always estimate gas requirements **before inscribing**, and consider padding
strategically. The key principle: **execution cost is deterministic and
proportional to the resources you allocate in Bitcoin**.
