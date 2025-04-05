# BitSwap DEX: Bitcoin-Secured Decentralized Exchange

[![Built with Clarity](https://img.shields.io/badge/Built%20with-Clarity-blue)](https://clarity-lang.org/)

BitSwap is an enterprise-grade decentralized exchange protocol leveraging Bitcoin's security model through native integration with Stacks Layer 2. This implementation provides a non-custodial trading environment with Bitcoin-finalized liquidity pools and MEV-resistant trading infrastructure.

## Key Features

- **Bitcoin-Secured Liquidity Pools**

  - Non-custodial pool reserves anchored to Bitcoin block finality
  - Clarity smart contracts with formal verification
  - Bitcoin-settled liquidity positions

- **Advanced AMM Design**

  - Constant product market maker (x\*y=k) implementation
  - Dynamic fees (0.3%-5%) with protocol-controlled parameters
  - Slippage-protected swaps with deadline enforcement

- **Stacks L2 Optimized**

  - Microblock-accelerated transactions (<30s finality)
  - BTC-denominated gas fees via Stacks transactions
  - Bitcoin-native yield opportunities

- **Security First**
  - Miner-extractable value (MEV) resistance
  - Overflow-safe mathematical operations
  - Price oracle manipulation resistance
  - Principal-ordered token pair system

## Technical Specifications

### Core Components

1. **Liquidity Pools**

   - Dual-asset pools with SIP-010 token support
   - Geometric mean initialization for first liquidity
   - Proportional share calculation for subsequent deposits

2. **Automated Market Maker**

   - Constant product formula: `x * y = k`
   - Fee structure: `0.3%` base fee (configurable)
   - Optimized swap routing with exact input/output support

3. **Fee Mechanism**

   - Protocol fee address (configurable)
   - Dynamic fee percentage (0.01%-5% in 0.01% increments)
   - Fee accrual in native token of input asset

4. **Oracle System**
   - Time-weighted average price (TWAP) tracking
   - Cumulative price mechanism
   - Bitcoin-block anchored price updates

## Contract Functions

### Pool Management

| Function           | Parameters                                                             | Description                                |
| ------------------ | ---------------------------------------------------------------------- | ------------------------------------------ |
| `create-pool`      | `token-a`, `token-b`                                                   | Creates new liquidity pool for token pair  |
| `add-liquidity`    | `token-a`, `token-b`, `amount-a`, `amount-b`, `min-shares`, `deadline` | Deposit liquidity with slippage protection |
| `remove-liquidity` | `token-a`, `token-b`, `shares`, `min-amounts`, `deadline`              | Withdraw liquidity proportionally          |

### Trading Operations

| Function                       | Parameters                                                  | Description                                    |
| ------------------------------ | ----------------------------------------------------------- | ---------------------------------------------- |
| `swap-exact-tokens-for-tokens` | `token-in`, `token-out`, `amount-in`, `min-out`, `deadline` | Execute fixed-input swap with price protection |

### Administrative

| Function             | Parameters               | Description                             |
| -------------------- | ------------------------ | --------------------------------------- |
| `set-fee-percentage` | `new-fee` (basis points) | Update protocol fee (owner-only)        |
| `set-fee-address`    | `new-address`            | Set fee collection address (owner-only) |

### View Functions

| Function               | Parameters           | Returns                      |
| ---------------------- | -------------------- | ---------------------------- |
| `get-reserves`         | `token-a`, `token-b` | Pool reserve balances        |
| `get-price`            | `token-a`, `token-b` | Current price ratio          |
| `get-protocol-metrics` | -                    | Total volume, fees, pools    |
| `get-provider-shares`  | `pool-id`, `address` | LP's share in specified pool |

## Usage Examples

### 1. Create Liquidity Pool

```clarity
(contract-call? .bitswap-dex create-pool token-a-contract token-b-contract)
```

### 2. Add Liquidity

```clarity
(contract-call? .bitswap-dex add-liquidity
  token-a-contract
  token-b-contract
  u1000000  ;; 1.0 token A
  u2000000  ;; 2.0 token B
  u950000   ;; Minimum 950k shares
  u180000   ;; 30min deadline
)
```

### 3. Execute Swap

```clarity
(contract-call? .bitswap-dex swap-exact-tokens-for-tokens
  .token-stx
  .token-btc
  u500000   ;; 0.5 STX input
  u4900     ;; Min 4900 sats output
  u180000   ;; Deadline block
)
```

## Security Model

### Audited Features

- Overflow/underflow protection
- Reentrancy guards
- Slippage bounds enforcement
- Deadline validation
- Pool existence checks
- Principal-ordered token pairs

### Error Codes

| Code | Description            |
| ---- | ---------------------- |
| u100 | Owner-only function    |
| u101 | Invalid LP provider    |
| u102 | Insufficient balance   |
| u103 | Zero liquidity         |
| u104 | Pool exists            |
| u105 | Pool not found         |
| u106 | Slippage exceeded      |
| u107 | Deadline passed        |
| u108 | Zero amount            |
| u109 | Same token pair        |
| u110 | Zero shares            |
| u111 | Insufficient liquidity |
| u112 | Invalid percentage     |
| u113 | Division by zero       |
