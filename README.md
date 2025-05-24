# BitHarvest - Bitcoin-Native DeFi Aggregation Platform

**BitHarvest** is a decentralized finance (DeFi) aggregation layer built on Stacks that enables trustless yield optimization across Bitcoin and Stacks ecosystems. The platform combines automated vault strategies with Bitcoin's security model, offering institutional-grade risk management and cross-protocol portfolio tracking.

## Key Features

- 🛡️ **Bitcoin-Native Security** - Inherits Bitcoin's proof-of-work security through Stacks L2
- 📈 **Smart Vault Strategies** - Automated yield farming across multiple protocols
- ⚖️ **Dynamic Rebalancing** - AI-driven allocation optimization with risk constraints
- 🔒 **Institutional Risk Controls** - Customizable liquidation alerts and LTV monitoring
- 📊 **Unified Dashboard** - Cross-protocol position tracking with real-time analytics
- ⚡ **Gasless Transactions** - Batch operations across protocols in single transaction

## Architecture Overview

### Core Components

1. **Protocol Adapters**
   - Modular connectors for DeFi protocols (Lending, DEXs, Derivatives)
   - Supports Bitcoin-based protocols (Ark, Liquid) and Stacks ecosystems
   - Trustless verification of protocol interactions

2. **Vault Engine**
   - Strategy templates (Stablecoin Yield, BTC Straddle, Volatility Harvesting)
   - Automated capital allocation/rebalancing
   - Performance fee mechanism (2-20 model)

3. **Risk Management Module**
   - Real-time exposure monitoring
   - Cross-margin liquidation protection
   - Insurance fund integration

4. **Portfolio Tracker**
   - Unified position tracking across protocols
   - Net Asset Value (NAV) calculator
   - Tax-optimized reporting

5. **Gasless Transaction Engine**
   - Meta-transaction bundler
   - STX-sponsored transactions
   - Atomic cross-protocol operations

## Smart Contract Structure

```clarity
;; Core Data Structures
- Protocols Registry → Map(protocol-id → protocol-config)
- Vault Strategies → Map(vault-id → strategy-params)
- User Positions → Nested maps tracking cross-protocol exposure
- Risk Parameters → Protocol-specific LTV ratios and liquidation thresholds
```

### Key Function Categories

1. **Admin Functions**
   - Protocol registration/management
   - System parameter configuration
   - Emergency circuit breakers

2. **Vault Management**
   - Vault creation/deposit/withdrawal
   - Strategy rebalancing hooks
   - Performance fee calculations

3. **Risk Management**
   - Position health monitoring
   - Margin call execution
   - Insurance fund claims

4. **Portfolio Operations**
   - Position synchronization
   - Cross-protocol netting
   - Historical P&L tracking

5. **Batch Processing**
   - Transaction sequence validation
   - Gas sponsorship management
   - Atomic cross-protocol execution

## Security Model

### Key Protections

- **Bitcoin Finality**: All critical operations inherit Bitcoin block finality
- **Two-Phase Commit**: For cross-protocol transactions
- **Circuit Breakers**:
  - Maximum drawdown limits
  - Volatility-based position caps
- **Formal Verification**: All strategies mathematically verified
- **Time Locks**: Critical parameter changes require 48-hour delay

```clarity
;; Example Security Check
(define-private (validate-withdrawal (vault-id uint) (amount uint))
  (asserts! (<= amount (get vault-max-withdrawal vault-id)) 
    ERR_EXCEEDS_SAFE_LIMIT)
  (asserts! (is-above-min-reserves vault-id amount)
    ERR_BELOW_PROTOCOL_MINIMUM)
)
```

## Getting Started

### Prerequisites

- Stacks CLI v3.0+
- Bitcoin testnet node
- Clarinet SDK

### Deployment

1. Clone repository:

   ```bash
   git clone https://github.com/dele-bami/bit-harvest.git
   cd bit-harvest
   ```

2. Install dependencies:

   ```bash
   clarinet install
   ```

3. Run test suite:

   ```bash
   clarinet test --cov
   ```

4. Deploy to testnet:

   ```bash
   clarinet deploy --testnet
   ```

## Integration Guide

### Connecting a New Protocol

1. Implement Protocol Adapter:

   ```clarity
   (impl-trait .protocol-interface.lending-pool-trait)
   
   (define-public (deposit (amount uint))
   (define-public (withdraw (amount uint))
   (define-read-only (get-apy))
   ```

2. Register Protocol:

   ```clarity
   (contract-call? .bitharvest-core register-protocol
     "New Lending Protocol"
     .new-lending-pool
     (list "BTC" "STX")
     "lending"
   )
   ```

3. Add to Vault Strategies:

   ```clarity
   (define-strategy "yield-v2" (
     (allocations (list
       { protocol-id: 1, weight: 60 }
       { protocol-id: 5, weight: 40 }
     ))
     (risk-params { max-drawdown: 15 })
   )
   ```
