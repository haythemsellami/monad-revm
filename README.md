# Monad REVM

![Monad REVM Banner](monad-revm-banner.png)

Monad-revm extends the [Revm](https://github.com/bluealloy/revm) Ethereum Virtual Machine with Monad-specific customizations and optimizations.

## EVM Version

| Component | Version | Link |
|-----------|---------|------|
| **Revm** | v34.0.0 | [bluealloy/revm v103](https://github.com/bluealloy/revm/releases/tag/v103) |
| **Ethereum Spec** | Prague | [EIPs](https://github.com/ethereum/EIPs) |
| **Monad Spec** | MONAD_EIGHT | [Revisions](https://docs.monad.xyz/developer-essentials/changelog/#revisions) |

## Features Checklist

- [x] **Custom Gas Pricing** - Cold storage access costs 8,100 gas (vs Ethereum's 2,100), cold account access costs 10,100 gas (vs 2,600)
- [x] **No Gas Refunds** - Refund counter is always zero; users pay `gas_limit × gas_price`, not `gas_used × gas_price`
- [x] **Repriced Precompiles** - ecRecover (2x), ecAdd (2x), ecMul (5x), ecPairing (5x), blake2f (2x), point evaluation (4x)
- [x] **P256 Precompile (secp256r1)** - RIP-7212 support at address `0x0100`
- [x] **Increased Bytecode Limits** - 128KB max code size, 256KB max initcode size
- [x] **Blob Transaction Rejection** - EIP-4844 transactions are not supported
- [ ] **Reserve Balance** - Minimum balance requirements for delegated EOAs
- [ ] **EIP-7702 Restrictions** - CREATE/CREATE2 banned for delegated EOAs
- [ ] **Monad-specific Precompiles** - Staking and other native precompiles

## Usage

Add to your `Cargo.toml`:

```toml
[dependencies]
monad-revm = { git = "https://github.com/haythemsellami/monad-revm", branch = "main" }
```

Basic example:

```rust,ignore
use monad_revm::{MonadContext, MonadHandler, DefaultMonad};
use revm::database::InMemoryDB;

// Create a Monad EVM instance
let db = InMemoryDB::default();
let mut evm = db.monad();

// Execute a transaction
let result = evm.transact(tx);
```

## Architecture

```
monad-revm/
├── crates/
│   └── monad-revm/
│       ├── src/
│       │   ├── lib.rs          # Re-exports
│       │   ├── spec.rs         # MonadSpecId hardfork enum
│       │   ├── cfg.rs          # MonadCfgEnv
│       │   ├── handler.rs      # MonadHandler
│       │   ├── instructions.rs # Custom gas parameters
│       │   ├── precompiles.rs  # Repriced precompiles
│       │   ├── evm.rs          # MonadEvm wrapper
│       │   └── api/            # Builder and execution traits
│       └── Cargo.toml
└── Cargo.toml                  # Workspace configuration
```

## Related Projects

- [revm](https://github.com/bluealloy/revm) - Base Ethereum Virtual Machine
- [alloy-monad-evm](https://github.com/haythemsellami/evm) - Alloy EVM wrapper for monad-revm
- [monad-foundry](https://github.com/haythemsellami/monad-foundry) - Foundry fork with Monad support