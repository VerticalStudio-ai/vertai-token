This document provides an overview of the VerticalAI smart contract. It explains the contract’s functionality, structure, and key features for registration and legal review purposes.

---

## Overview

The **VerticalAI** contract is an ERC20-compliant token with additional functionalities designed to support automated liquidity management and fee processing on decentralized exchanges (DEX), specifically Uniswap V2. The contract integrates fee mechanisms for marketing purposes and includes built-in controls to manage trading conditions.

---

## Key Features

- **ERC20 Token Implementation:**  
  Inherits standard ERC20 functionality including token transfers, approvals, minting, and burning. The token has 18 decimals and is constructed with a fixed total supply, minus the initial liquidity amount provided during deployment.

- **Ownership & Access Control:**  
  The contract uses an Ownable pattern to restrict sensitive operations (e.g., enabling trading, adjusting fees, claiming stuck tokens) to the contract owner.

- **Fee Mechanism:**  
  - A configurable marketing fee is applied to token transfers under certain conditions (e.g., during liquidity transactions on Uniswap).
  - Fees are accumulated in the contract and later swapped for ETH, which is then sent to a designated marketing fee receiver.

- **Uniswap Integration:**  
  - Interfaces for Uniswap V2 Router and Factory are implemented to facilitate liquidity pair creation and token-to-ETH swapping.
  - Trading must be explicitly enabled by the owner, which creates the Uniswap pair, adds liquidity, and sets up fee processing.
  
- **Trading & Wallet Limits:**  
  - Trading is disabled by default and must be enabled by the owner.
  - A maximum wallet size is enforced to prevent any address from holding more than a specified amount of tokens.
  - A maximum swap threshold is defined to limit the token amount processed in a single fee conversion.

- **Exclusion Mechanism:**  
  Certain addresses (e.g., owner, contract address, dead address, fee receiver, and liquidity pair) can be excluded from fees to facilitate administrative and liquidity operations.

---

## Contract Structure

### Interfaces

- **IUniswapV2Factory, IUniswapV2Router01, IUniswapV2Router02:**  
  These interfaces allow the contract to interact with the Uniswap V2 protocol for creating liquidity pairs and swapping tokens for ETH.

- **IERC20 & IERC20Metadata:**  
  Standard ERC20 interfaces that define the basic functions for token management.

### Libraries and Base Contracts

- **Address Library:**  
  Provides a utility function (`sendValue`) to safely transfer ETH from the contract.

- **Context & Ownable Contracts:**  
  - **Context:** Provides information about the current execution context, such as the message sender.  
  - **Ownable:** Establishes a basic access control mechanism, allowing only the owner to perform administrative actions.

### ERC20 Contract Implementation

The `ERC20` contract implements:
- Token details (name, symbol, decimals)
- Basic token operations: transfer, approve, transferFrom, mint, and burn
- Allowance and balance management for token holders

### VerticalAI Contract

The `VerticalAI` contract extends the ERC20 and Ownable contracts. Its primary functions include:
- **Token Distribution:**  
  Minting tokens for the owner and the contract (used for liquidity).
  
- **Fee Management:**  
  Processing marketing fees during token transfers. Fees are collected, swapped for ETH, and forwarded to the marketing fee receiver.
  
- **Trading Control:**  
  - **Enable Trading:** Initializes liquidity on Uniswap by creating a token pair, adding liquidity, and approving spending.
  - **Toggle Swapping:** Allows the owner to enable or disable the swapping mechanism.
  
- **Administrative Functions:**  
  Functions to update fee receiver addresses, adjust fee percentages (with a lowering-only constraint), update maximum wallet size, and change the swap threshold.

- **Safety Measures:**  
  The contract prevents trading until it is explicitly enabled and enforces wallet size limits to ensure controlled token distribution.

---

## How It Works

1. **Deployment:**  
   - The contract is deployed with a specified Uniswap V2 Router address, a marketing fee receiver address, and an initial liquidity amount.
   - The owner receives the majority of the token supply, while a portion is reserved for providing initial liquidity.

2. **Trading Enablement:**  
   - Initially, trading is disabled to prevent unauthorized transfers.
   - The owner must call `enableTrading()`, which creates a Uniswap liquidity pair, adds liquidity, and permits trading.

3. **Transaction Processing:**  
   - When a transfer occurs, if trading is enabled and the involved addresses are not excluded from fees, a marketing fee is deducted.
   - The contract accumulates fees and, upon reaching a threshold, swaps tokens for ETH to fund the marketing wallet.

4. **Administrative Adjustments:**  
   - The owner can adjust critical parameters such as fee rates, swap thresholds, and wallet limits.
   - Functions are available to recover tokens or ETH accidentally sent to the contract, except for the native token itself.

---

## Deployment & Usage

- **Solidity Version:**  
  The contract is written in Solidity 0.8.19.
  
- **Constructor Parameters:**  
  - `_router`: Address of the Uniswap V2 Router.
  - `_feeReceiver`: Address where marketing fees will be sent.
  - `_initalLiquidity`: Amount of tokens reserved for initial liquidity.
  
- **Post-Deployment Steps:**  
  1. Deploy the contract with the appropriate parameters.
  2. Exclude necessary addresses from fees using `excludeFromFees()`.
  3. Enable trading by calling `enableTrading()` once ready.

---

## Legal & Compliance Considerations

- **License:**  
  The contract is marked with an SPDX license identifier as **UNLICENSED**, indicating that it is not available under an open-source license.
