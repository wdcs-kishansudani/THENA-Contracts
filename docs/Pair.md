# PairFactory.sol and Pair.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `PairFactory.sol` and `Pair.sol` contracts.

## `PairFactory.sol`

### `createPair(address tokenA, address tokenB, bool stable)`

-   **Purpose:** This function is used to create a new liquidity pool for a pair of tokens. It is the entry point for adding new trading pairs to the protocol.
-   **Parameters:**
    -   `tokenA`: The address of the first token in the pair.
    -   `tokenB`: The address of the second token in the pair.
    -   `stable`: A boolean that determines whether the pool will use the stable curve (`x^3y + y^3x = k`) or the volatile curve (`x*y = k`).
-   **Returns:** The address of the newly created `Pair` contract.
-   **Interaction:** This function deploys a new `Pair` contract using the `CREATE2` opcode, which allows for deterministic addresses.

### `getPair(address tokenA, address tokenB, bool stable)`

-   **Purpose:** This function retrieves the address of an existing liquidity pool.
-   **Parameters:**
    -   `tokenA`: The address of the first token in the pair.
    -   `tokenB`: The address of the second token in the pair.
    -   `stable`: A boolean that specifies whether to look for a stable or volatile pool.
-   **Returns:** The address of the `Pair` contract, or the zero address if it does not exist.

### `setFee(bool _stable, uint256 _fee)`

-   **Purpose:** This function allows the fee manager to set the trading fee for stable or volatile pools.
-   **Parameters:**
    -   `_stable`: A boolean that specifies whether to set the fee for stable or volatile pools.
    -   `_fee`: The new fee in basis points (e.g., 25 for 0.25%).
-   **Interaction:** The fee set by this function is used in the `Pair.sol` contract to calculate the fees for each swap.

## `Pair.sol`

### `swap(uint amount0Out, uint amount1Out, address to, bytes calldata data)`

-   **Purpose:** This is the core function for executing a token swap. It is called by the `RouterV2` contract.
-   **Parameters:**
    -   `amount0Out`: The amount of `token0` to send to the recipient.
    -   `amount1Out`: The amount of `token1` to send to the recipient.
    -   `to`: The address of the recipient.
    -   `data`: Optional data that can be passed to a contract call, which is useful for flash loans.
-   **Interaction:** This function updates the reserves of the pool, calculates the fees, and transfers the output tokens to the recipient.

### `mint(address to)`

-   **Purpose:** This function is called by the `RouterV2` contract to mint liquidity provider (LP) tokens when a user adds liquidity to the pool.
-   **Parameters:**
    -   `to`: The address that will receive the LP tokens.
-   **Returns:** The amount of LP tokens minted.
-   **Interaction:** This function calculates the amount of LP tokens to mint based on the amount of tokens deposited and the current reserves.

### `burn(address to)`

-   **Purpose:** This function is called by the `RouterV2` contract to burn LP tokens when a user removes liquidity from the pool.
-   **Parameters:**
    -   `to`: The address that will receive the underlying tokens.
-   **Returns:** The amounts of `token0` and `token1` that were returned to the user.
-   **Interaction:** This function calculates the amount of underlying tokens to return based on the amount of LP tokens burned and the current reserves.

### `getReserves()`

-   **Purpose:** This function returns the current reserves of the pool, which are used to calculate the prices of the tokens.
-   **Returns:**
    -   `_reserve0`: The reserve of `token0`.
    -   `_reserve1`: The reserve of `token1`.
    -   `_blockTimestampLast`: The timestamp of the last block in which the reserves were updated.
-   **Interaction:** This function is called by the `RouterV2` contract to get the reserves for calculating swap amounts and by the TWAP oracle to calculate time-weighted average prices.
