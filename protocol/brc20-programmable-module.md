---
title: "BRC20 Programmable Module"
---

BRC2.0 extends BRC-20's inscription-based token standard with an
**EVM-compatible execution layer**, enabling smart contracts, DeFi primitives,
and composable applications on Bitcoin. While BRC-20 provides token operations
(deploy, mint, transfer), BRC2.0 provides a **programmable computing
environment** where Solidity contracts can execute with full access to Bitcoin's
state.

## **What BRC2.0 Adds to BRC-20**

BRC2.0 introduces four major capabilities:

**Smart contracts**: Deploy Solidity contracts inscribed on Bitcoin, with state
transitions validated by indexers running EVM execution **Deposit/Withdraw
bridge**: Move BRC-20 tokens into BRC2.0 contracts as ERC-20-compatible assets
**Bitcoin precompiles**: Native access to Bitcoin transaction verification,
signature validation (BIP322), and UTXO queries **Event system**: Contract logs
that applications can subscribe to for real-time updates

The core insight: **BRC2.0 maintains the metaprotocol model**—execution happens
off Bitcoin's base layer with results tracked by indexers—but adds computational
expressiveness previously impossible with BRC-20 alone.

## **Architecture and Execution Model**

BRC2.0 indexers run an EVM-compatible execution environment alongside
traditional BRC-20 state tracking. When you inscribe a BRC2.0 operation (deploy
contract, call function, deposit tokens), indexers:

1. **Parse the inscription**: Extract BRC2.0 JSON from the Ordinals envelope
2. **Validate the operation**: Check signature, nonce, and gas limits
3. **Execute EVM bytecode**: Run the transaction in their local EVM instance
4. **Update state**: Commit storage changes, logs, and balance updates
5. **Achieve consensus**: All indexers must produce identical state transitions

This creates a **deterministic state machine** whose history is anchored in
Bitcoin inscriptions but whose execution happens off-chain.

### **State Separation**

BRC2.0 maintains two distinct state domains:

**BRC-20 state**: Traditional token balances tracked by BRC-20 inscriptions

**BRC2.0 contract state**: EVM storage, contract accounts, and internal token
balances

You can move assets between these domains using deposit/withdraw operations,
creating a bridge between Bitcoin-native tokens and smart contract applications.

## **BRC2.0 Operation Types**

BRC2.0 defines three new operation types beyond BRC-20's deploy/mint/transfer:

### **Deploy Operation**

Deploy a Solidity contract to the BRC2.0 execution layer:

```json
{
  "p": "brc20-prog",
  "op": "deploy", // or d for short
  "d": "0x608060405234801561001057600080fd5b50...",
}
```

The bytecode is compiled Solidity (use Hardhat, Foundry, or Remix). Indexers
execute the constructor and assign the contract an address derived from the
inscription ID.

### **Call Operation**

Invoke a function on a deployed contract:

```json
{
  "p": "brc20-prog",
  "op": "call", // or c for short
  "c": ",
  "b": "”
}
```

This triggers EVM execution of the specified function with provided arguments.
Indexers validate gas limits and update contract state accordingly.

### **Transact Operation**

Combined deposit/withdraw with contract call—move BRC-20 tokens into a contract
and invoke a function in one operation:

```json
{
  "p": "brc20-prog",
  "op": "transact", // or t for short
  "b": "
}
```

This is the primary pattern for DeFi interactions: deposit tokens, execute
trade/stake/lend, and optionally withdraw results.

## **Deposit and Withdraw Mechanics**

BRC2.0 includes a **built-in bridge** for moving BRC-20 tokens into smart
contracts:

### **Deposit Flow**

1. **Create deposit inscription**: Specify ticker and amount to deposit
2. **Indexers lock BRC-20 balance**: Tokens move from BRC-20 state to
   "deposited" state
3. **Mint internal BRC-20**: Contract receives equivalent tokens as ERC-20
   balance
4. **You can use tokens**: Trade, stake, lend, etc. using Solidity logic

Deposit 1000 ORDI into BRC2.0

```json
{
  "p": "brc-20",
  "op": "deposit",
  "tick": "ordi",
  "amt": "1000"
}
```

Sending this inscription to OP_RETURN "BRC20PROG" will transfer your ORDI into
BRC2.0 programmable module.

### **Withdraw Flow**

Create an inscription with the amount to withdraw

```json
{
  "p": "brc20-module",
  "op": "withdraw",
  "tick": "ordi",
  "amt": "10",
  "module": "BRC20PROG"
}
```

Sending this to any address other than `OP\_RETURN` will withdraw the balance
from BRC2.0 into base BRC20

This creates a **trustless bridge**—no multisig or validator set required.
Indexers enforce the rules deterministically based on inscriptions and EVM
execution.

## **Precompiles**

BRC2.0 introduces specialized precompiled contracts for Bitcoin-native
operations:

### BIP-322 Verifier Precompile

EVM addresses are generated from Bitcoin scripts, and they can’t sign messages,
instead, you can use a precompile to validate BIP-322 signatures instead using
this precompile:

```solidity
// Precompile address at 0xFE
/**
 * @dev BIP322 verification method
 */
interface IBIP322_Verifier {
    function verify(
        bytes calldata pkscript,
        bytes calldata message,
        bytes calldata signature
    ) external returns (bool success);
}
```

Use cases: Caller verification, authentication and authorization using Bitcoin
wallets

### **BTC Transaction Verification Precompile**

Verify Bitcoin transactions and extract data from them:

```solidity
// Precompile address at 0xFD
/**
 * Get Bitcoin transaction details using tx ids.
 */
interface IBTC_Transaction {
    function getTxDetails(
        bytes32 txid
    )
        external
        view
        returns (
            uint256 block_height,
            bytes32[] memory vin_txids,
            uint256[] memory vin_vouts,
            bytes[] memory vin_scriptPubKeys,
            uint256[] memory vin_values,
            bytes[] memory vout_scriptPubKeys,
            uint256[] memory vout_values
        );
}
```

Use case: **Trustless Bitcoin payment verification**—contracts can confirm that
specific Bitcoin transactions occurred, enabling payment-triggered logic.

### Last Satoshi Location Precompile

Find the last location of an individual satoshi

```solidity
// Precompile address at 0xFC
/**
 * @dev Get last satoshi location of a given sat location in a transaction.
 */
interface IBTC_LastSatLoc {
    function getLastSatLocation(
        bytes32 txid,
        uint256 vout,
        uint256 sat
    ) external view returns (
        bytes32 last_txid,
        uint256 last_vout,
        uint256 last_sat,
        bytes memory old_pkscript,
        bytes memory new_pkscript
    );
}
```

### **Lock PkScript Precompile**

Check if a BRC-20 token holder has locked their inscriptions to a specific
Bitcoin script:

```solidity
// Precompile address at 0xFB
/**
 * @dev Get locked pkscript of a given Bitcoin wallet script.
 */
interface IBTC_LockedPkscript {
    function getLockedPkscript(
        bytes calldata pkscript,
        uint256 lock_block_count
    ) external view returns (bytes memory locked_pkscript);
}
```

Use case: **Staking and governance**—users lock ordinals on Bitcoin to prove
commitment, contracts verify the lock status and grant voting rights or rewards.

### **BIP322 Signature Precompile**

Verify BIP-322 signatures (generic Bitcoin message signing):

```solidity
// Precompile at address 0xFE
/**
 * @dev BIP322 verification method
 */
interface IBIP322_Verifier {
    function verify(
        bytes calldata pkscript,
        bytes calldata message,
        bytes calldata signature
    ) external returns (bool success);
}
```

Use case: **Bitcoin identity verification**—users sign messages with their
Bitcoin private keys, contracts verify signatures to authorize actions without
requiring users to hold Ethereum-style keys.

These precompiles bridge Bitcoin's UTXO model with BRC2.0's account model,
enabling hybrid applications that leverage both ecosystems.

## OP_RETURN TX ID precompile

Transaction that activates the BRC2.0 inscription (TX that sends the inscription
to OP_RETURN) can be retrieved from a smart contract using this precompile:

```solidity
// Precompile at address 0xFA
interface TxIdPrecompile {
    function getBtcTxId() external view returns (bytes32);
}
```

Use cases: Binding contract state or events to a specific bitcoin transaction,
deterministic linkage between on-chain btc activity and evm-level logic,
implementing receipt-style verification, replay protection, or audit trails

## **EVM Compatibility and Limitations**

BRC2.0 aims for Solidity compatibility but has key differences from Ethereum:

**Supported**:

- Solidity syntax and features
- Standard library contracts (OpenZeppelin, etc.)
- Events and logs
- Storage operations (SSTORE, SLOAD)
- Standard opcodes (ADD, MUL, CALL, etc.)

**Modified**:

- Block time: ~10 minutes (Bitcoin's block interval)
- Gas model: Different gas costs due to indexer execution constraints
- Address format: Uses generated EVM addresses from Bitcoin-native addresses
  (bc1p...), hence, the EVM addresses cannot sign messages
- block.number refers to Bitcoin block height
- block.timestamp is Bitcoin block time (less granular), bitcoin block times can
  go backwards, so this is a less reliable metric
- msg.value in native ETH doesn't exist (use token deposits instead)

### **Gas Model**

BRC2.0 uses a **Bitcoin-anchored gas model**:

- Gas limits prevent infinite loops during indexer execution
- If execution exceeds gas limit, the transaction reverts and state is not
  updated—standard EVM behavior.

## **When to Use BRC2.0 vs BRC-20**

**Use BRC-20 when**:

- Simple token operations suffice (hold, send, receive)
- You want maximum compatibility with existing wallets/tools
- Lower complexity and gas costs are priorities
- No programmable logic is required

**Use BRC2.0 when**:

- Building DeFi protocols (DEXes, lending, staking)
- Requiring conditional logic or multi-party interactions
- Implementing governance or DAO functionality
- Need access to Bitcoin state (transaction verification, locks)
- Building composable applications that interact with other contracts

## **Example: DEX Implementation**

A simple constant product AMM in BRC2.0:

```solidity
contract SimpleDEX {
    mapping(address => uint256) public balancesA;
    mapping(address => uint256) public balancesB;
    uint256 public reserveA;
    uint256 public reserveB;
    
    event Swap(address indexed user, uint256 amountIn, uint256 amountOut);
    
    function deposit(string calldata tick, uint256 amount) external {
        // Called via BRC2.0 deposit operation
        if (keccak256(bytes(tick)) == keccak256("ordi")) {
            balancesA[msg.sender] += amount;
            reserveA += amount;
        } else if (keccak256(bytes(tick)) == keccak256("sats")) {
            balancesB[msg.sender] += amount;
            reserveB += amount;
        }
    }
    
    function swap(uint256 amountIn, bool aToB) external {
        uint256 amountOut;
        if (aToB) {
            require(balancesA[msg.sender] >= amountIn, "Insufficient balance");
            amountOut = getAmountOut(amountIn, reserveA, reserveB);
            balancesA[msg.sender] -= amountIn;
            balancesB[msg.sender] += amountOut;
            reserveA += amountIn;
            reserveB -= amountOut;
        } else {
            require(balancesB[msg.sender] >= amountIn, "Insufficient balance");
            amountOut = getAmountOut(amountIn, reserveB, reserveA);
            balancesB[msg.sender] -= amountIn;
            balancesA[msg.sender] += amountOut;
            reserveB += amountIn;
            reserveA -= amountOut;
        }
        emit Swap(msg.sender, amountIn, amountOut);
    }

    function withdraw(string calldata tick, uint256 amount) external {
        // Burns internal balance, indexer credits BRC-20
        if (keccak256(bytes(tick)) == keccak256("ordi")) {
            require(balancesA[msg.sender] >= amount);
            balancesA[msg.sender] -= amount;
            reserveA -= amount;
        } else {
            require(balancesB[msg.sender] >= amount);
            balancesB[msg.sender] -= amount;
            reserveB -= amount;
        }
        emit Withdraw(msg.sender, tick, amount);
    }

    function getAmountOut(uint256 amountIn, uint256 reserveIn, uint256 reserveOut)
        internal pure returns (uint256) 
    {
        uint256 amountInWithFee = amountIn * 997;
        uint256 numerator = amountInWithFee * reserveOut;
        uint256 denominator = (reserveIn * 1000) + amountInWithFee;
        return numerator / denominator;
    }
}
```

Users interact by:

1. Depositing ORDI or SATS into the contract (moves from BRC-20 to contract
   state)
2. Calling swap() to trade (pure EVM execution, instant)
3. Withdrawing back to BRC-20 balance (returns to Bitcoin-native tokens)

This is impossible with BRC-20 alone—you'd need centralized order books and
manual matching. BRC2.0 enables automatic execution with deterministic pricing.

## **Indexer Requirements and Node Operation**

Running a BRC2.0 indexer requires:

**Bitcoin full node**: ~600GB storage, validates all transactions **Ordinals
indexer**: Tracks inscription data and satoshi ownership **EVM execution
engine**: Modified geth or similar (provided by BRC2.0 implementation) **State
database**: Stores BRC-20 and BRC2.0 state (PostgreSQL, LevelDB, etc.)

Most developers will **query public indexers** rather than running their own:

- Lower operational complexity
- No infrastructure costs
- Faster time to market

Run your own indexer if you need:

- Custom validation rules
- High query throughput
- Independence from third-party services
- Ability to audit state transitions

## **Security Model and Trust Assumptions**

BRC2.0 inherits BRC-20's trust model with additional considerations:

**Immutability**: Contract bytecode inscribed on Bitcoin cannot be changed
**Determinism**: All indexers must produce identical state given same
inscriptions **Social consensus**: If indexers diverge, community must choose
canonical implementation **Upgradability**: Use proxy patterns (similar to
Ethereum) for upgradeable contracts **Precompile trust**: Bitcoin verification
precompiles rely on correct implementation in indexer code

Critical insight: **BRC2.0 security depends on indexer correctness**. If the EVM
implementation has bugs or deviates from specification, contract execution may
diverge across indexers. This is why reference implementations and test suites
are essential.

## **Comparison to Other Bitcoin Smart Contract Solutions**

**BRC2.0 vs Lightning Network**:

- Lightning: Payment channels for Bitcoin transfers (no tokens, no arbitrary
  computation)
- BRC2.0: Full smart contracts with token support

**BRC2.0 vs Stacks**:

- Stacks: Separate blockchain with its own consensus (Proof of Transfer)
- BRC2.0: Metaprotocol anchored directly to Bitcoin inscriptions

**BRC2.0 vs Rootstock (RSK)**:

- RSK: Sidechain with merge-mining and 2-way peg bridge
- BRC2.0: No separate chain, state tracked by indexers

**BRC2.0 vs RGB**:

- RGB: Client-side validation for asset transfers
- BRC2.0: Server-side (indexer) validation with EVM compatibility

## **Development Workflow**

Typical BRC2.0 development process:

1. **Write Solidity contracts**: Use Hardhat, Foundry, or Remix as usual
2. **Test locally**: Deploy to local BRC2.0 node or testnet
3. **Compile to bytecode**: Generate deployment bytecode and ABI
4. **Inscribe contract**: Create deploy inscription with bytecode
5. **Wait for confirmation**: Bitcoin block must confirm (~10 minutes)
6. **Interact via inscriptions**: Call functions by inscribing call operations
7. **Monitor events**: Subscribe to indexer APIs for contract logs

The cycle time is slower than Ethereum due to Bitcoin's block interval, but
inscriptions provide permanent on-chain history of all interactions.

## **Ecosystem Readiness**

As of launch (2025), BRC2.0 is in early stages with growing infrastructure:

**Available**:

- Reference indexer implementation
- Solidity compiler support
- Block explorers for contracts
- Developer documentation and SDKs

**In development**:

- Wallet integrations (MetaMask-style for BRC2.0)

Developers building on BRC2.0 today are pioneers—expect rough edges but also
opportunities to define standards and capture early ecosystem value.

To start building with BRC2.0:

1. Review the **BRC2.0 JSON Schema** documentation for operation formats
2. Explore **BRC2.0 Deposit/Withdraw** mechanics for token bridges
3. Study **Bitcoin Precompiles** for native Bitcoin integration
4. Build a **simple contract** following the guides

BRC2.0 represents the evolution of Bitcoin from settlement layer to programmable
platform, while maintaining the inscription-based guarantees that made BRC-20
successful.

**Further reading**:[BRC2.0 Specification](https://docs.brc20.build/)
|[Solidity Documentation](https://docs.soliditylang.org/)
