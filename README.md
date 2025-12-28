# DEX AMM Project

## Overview

This project implements a simplified Decentralized Exchange (DEX) using an Automated Market Maker (AMM) model similar to Uniswap V2. It enables decentralized token trading without order books or centralized intermediaries. Users can add liquidity, remove liquidity, swap tokens, and earn trading fees as liquidity providers.

The DEX uses the constant product formula (x \* y = k) to determine prices and ensure continuous liquidity.

---

## Features

- Initial and subsequent liquidity provision
- Liquidity removal with proportional share calculation
- Token swaps using constant product formula (x \* y = k)
- 0.3% trading fee for liquidity providers
- LP token minting and burning via internal liquidity accounting

---

## Architecture

The system consists of the following components:

- **DEX.sol**  
  Core smart contract implementing liquidity pools, swaps, pricing logic, and fee distribution.

- **MockERC20.sol**  
  Simple ERC-20 token contract used for testing purposes.

The DEX contract internally tracks token reserves and liquidity shares. Liquidity providers are issued proportional LP balances, which represent ownership of the pool. All swaps and liquidity actions update reserves explicitly to maintain consistency.

---

## Mathematical Implementation

### Constant Product Formula

The DEX follows the invariant:

x \* y = k

Where:

- x = reserve of Token A
- y = reserve of Token B
- k = constant product

After every swap, the product of reserves remains constant or increases slightly due to fees retained in the pool.

---

### Fee Calculation

Each swap applies a 0.3% trading fee.  
Only 99.7% of the input amount is used in the swap calculation.

Formula used:

amountInWithFee = amountIn _ 997  
numerator = amountInWithFee _ reserveOut  
denominator = (reserveIn \* 1000) + amountInWithFee  
amountOut = numerator / denominator

The deducted fee remains in the pool, increasing total liquidity and benefiting liquidity providers.

---

### LP Token Minting

#### Initial Liquidity

For the first liquidity provider:

liquidityMinted = sqrt(amountA \* amountB)

The first provider sets the initial price of the pool.

#### Subsequent Liquidity

Liquidity must match the existing reserve ratio.  
LP tokens are minted proportionally:

liquidityMinted = (amountA \* totalLiquidity) / reserveA

---

## Setup Instructions

### Prerequisites

- Docker and Docker Compose installed
- Git

---

### Installation

1. Clone the repository:
   git clone https://github.com/mallacharmi/dex-amm.git
   cd dex-amm

2. Start Docker environment:
   docker-compose up -d

3. Compile contracts:
   docker-compose exec app npm run compile

4. Run tests:
   docker-compose exec app npm test

5. Check coverage:
   docker-compose exec app npm run coverage

6. Stop Docker:
   docker-compose down

---

## Running Tests Locally (without Docker)

npm install  
npm run compile  
npm test

---

## Contract Addresses

This project is not deployed to a public testnet.  
Deployment can be performed using:

npm run deploy

---

## Known Limitations

- Supports only a single trading pair
- No slippage protection (minAmountOut)
- No deadline parameter for transactions
- No flash swaps

These features can be added as future enhancements.

---

## Security Considerations

- Solidity 0.8+ built-in overflow and underflow protection
- Input validation for zero values and insufficient liquidity
- Explicit reserve tracking to avoid balance desynchronization
- Reentrancy-safe design (state updates before external calls)
- Use of OpenZeppelin ERC-20 implementation for token safety
