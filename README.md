# Foundry Upgradeable Smart Contracts

This project demonstrates how to implement and manage upgradeable smart contracts using OpenZeppelin's UUPS (Universal Upgradeable Proxy Standard) pattern with Foundry.

## Table of Contents

- [Overview](#overview)
- [Technology Stack](#technology-stack)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
- [Deployment Process](#deployment-process)
- [Upgrading Contracts](#upgrading-contracts)
- [Testing](#testing)
- [Security Considerations](#security-considerations)

## Overview

This project implements an upgradeable smart contract system using the UUPS pattern. It includes:

- A BoxV1 contract with basic functionality
- A BoxV2 contract with extended functionality
- Deployment scripts for the initial implementation
- Upgrade scripts to update to newer versions

The main goal is to demonstrate how to maintain state while upgrading contract logic, a critical feature for many production-level DApps.

## Technology Stack

### Foundry

Foundry is a fast, portable, and modular toolkit for Ethereum application development written in Rust. Unlike Hardhat or Truffle, Foundry is designed with a focus on speed and flexibility. The toolkit includes:

- **Forge**: A testing framework for Ethereum smart contracts
- **Cast**: A command-line tool for interacting with smart contracts
- **Anvil**: A local Ethereum node for development and testing
- **Chisel**: A Solidity REPL (Read-Eval-Print Loop) for quick prototyping

### OpenZeppelin Contracts

This project leverages OpenZeppelin's battle-tested smart contract libraries:

- **ERC1967Proxy**: The proxy contract that delegates calls to the implementation contract
- **UUPSUpgradeable**: A pattern for upgradeable contracts where the upgrade logic is in the implementation contract
- **Initializable**: A base contract to handle initialization, replacing constructors
- **OwnableUpgradeable**: Access control to ensure only authorized addresses can perform upgrades

### Proxy Patterns (UUPS)

The Universal Upgradeable Proxy Standard (UUPS) is an upgrade pattern where:

- The upgrade logic resides in the implementation contract, not the proxy
- This reduces the proxy contract size, making deployment cheaper
- It allows for more flexibility in upgrade mechanisms
- Unlike Transparent Proxies, it doesn't have the storage collision issue between the proxy and implementation

## Project Structure

```
foundry-upgradable-contract/
├── lib/                      # External libraries
│   ├── forge-std/            # Standard Foundry utilities
│   ├── openzeppelin-contracts/         # Core OpenZeppelin contracts
│   └── openzeppelin-contracts-upgradeable/ # Upgradeable variants
├── src/                      # Source files
│   ├── BoxV1.sol             # Initial implementation
│   └── BoxV2.sol             # Upgraded implementation
├── script/                   # Deployment and upgrade scripts
│   ├── DeployBox.s.sol       # Script to deploy BoxV1
│   └── UpgradeBox.s.sol      # Script to upgrade to BoxV2
├── test/                     # Test files
├── foundry.toml              # Foundry configuration
└── README.md                 # This file
```

## Getting Started

### Prerequisites

- [Foundry installed](https://getfoundry.sh/)

### Installation

1. Clone this repository:

```bash
git clone https://github.com/yourusername/foundry-upgradable-contract.git
cd foundry-upgradable-contract
```

2. Install dependencies:

```bash
forge install
```

## Deployment Process

The deployment process involves two main steps:

1. Deploy the implementation contract (BoxV1)
2. Deploy the proxy that points to the implementation

This is all handled in the `DeployBox.s.sol` script.

To deploy to a local network:

```bash
forge script script/DeployBox.s.sol:DeployBox --rpc-url http://localhost:8545 --broadcast --private-key <YOUR_PRIVATE_KEY>
```

To deploy to a testnet:

```bash
forge script script/DeployBox.s.sol:DeployBox --rpc-url <RPC_URL> --broadcast --private-key <YOUR_PRIVATE_KEY>
```

## Upgrading Contracts

The upgrade process involves:

1. Deploy a new implementation contract (BoxV2)
2. Point the proxy to the new implementation

This is handled in the `UpgradeBox.s.sol` script.

To upgrade:

```bash
forge script script/UpgradeBox.s.sol:UpgradeBox --rpc-url <RPC_URL> --broadcast --private-key <YOUR_PRIVATE_KEY>
```

## Testing

To run the full test suite:

```bash
forge test -vvv
```

The `-vvv` flag provides verbose output, showing all test cases and their outcomes.

## Security Considerations

Upgradeable contracts come with specific security considerations:

1. **Initialization**: Unlike regular contracts, upgradeable contracts use initializer functions instead of constructors
2. **Storage Layout**: When upgrading, storage variables must be maintained in the same order
3. **Access Control**: Only authorized addresses should be able to trigger upgrades
4. **Implementation Contract Security**: The implementation contract should be initialized upon deployment to prevent front-running attacks

## Advanced Topics

### Storage Gaps

This project uses storage gaps (the `__gap` array in upgradeable contracts) to reserve storage slots for future versions, preventing storage collisions when adding variables.

### Upgrade Authorization

The BoxV1 and BoxV2 contracts use the `onlyOwner` modifier for upgrade authorization, limiting the upgrade capability to the contract owner.

### Initializer Locking

Both BoxV1 and BoxV2 lock their initializers in the constructor using the `_disableInitializers()` function, which prevents the implementation contracts from being initialized directly.

### Proxy Delegate Calls

The ERC1967Proxy uses delegate calls to execute the implementation contract's code within the proxy's storage context. This allows the proxy to maintain state while changing implementation.
