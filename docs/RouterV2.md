# RouterV2.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `RouterV2.sol` contract.

## `addLiquidity(address tokenA, address tokenB, bool stable, uint amountADesired, uint amountBDesired, uint amountAMin, uint amountBMin, address to, uint deadline)`

-   **Purpose:** This function is the primary entry point for users to add liquidity to a pool. It handles the creation of the pair if it doesn't exist and the calculation of the optimal amounts of tokens to deposit.
-   **Parameters:**
    -   `tokenA`: The address of the first token.
    -   `tokenB`: The address of the second token.
    -   `stable`: A boolean indicating whether the pool is for stable or volatile assets.
    -   `amountADesired`: The desired amount of `tokenA` to add.
    -   `amountBDesired`: The desired amount of `tokenB` to add.
    -   `amountAMin`: The minimum amount of `tokenA` to add, which protects against slippage.
    -   `amountBMin`: The minimum amount of `tokenB` to add, which protects against slippage.
    -   `to`: The address to receive the liquidity tokens.
    -   `deadline`: The deadline for the transaction.
-   **Returns:**
    -   `amountA`: The amount of `tokenA` actually added.
    -   `amountB`: The amount of `tokenB` actually added.
    -   `liquidity`: The amount of LP tokens minted.
-   **Interaction:** This function interacts with the `PairFactory` to create the pair if necessary, and with the `Pair` contract to deposit the tokens and mint the LP tokens.

## `removeLiquidity(address tokenA, address tokenB, bool stable, uint liquidity, uint amountAMin, uint amountBMin, address to, uint deadline)`

-   **Purpose:** This function is the primary entry point for users to remove liquidity from a pool.
-   **Parameters:**
    -   `tokenA`: The address of the first token.
    -   `tokenB`: The address of the second token.
    -   `stable`: A boolean indicating whether the pool is for stable or volatile assets.
    -   `liquidity`: The amount of LP tokens to burn.
    -   `amountAMin`: The minimum amount of `tokenA` to receive, which protects against slippage.
    -   `amountBMin`: The minimum amount of `tokenB` to receive, which protects against slippage.
    -   `to`: The address to receive the underlying assets.
    -   `deadline`: The deadline for the transaction.
-   **Returns:**
    -   `amountA`: The amount of `tokenA` returned.
    -   `amountB`: The amount of `tokenB` returned.
-   **Interaction:** This function interacts with the `Pair` contract to burn the LP tokens and withdraw the underlying assets.

## `swapExactTokensForTokens(uint amountIn, uint amountOutMin, route[] calldata routes, address to, uint deadline)`

-   **Purpose:** This function allows users to swap an exact amount of input tokens for a minimum amount of output tokens. It supports multi-hop swaps through an array of routes.
-   **Parameters:**
    -   `amountIn`: The amount of input tokens.
    -   `amountOutMin`: The minimum amount of output tokens to receive, which protects against slippage.
    -   `routes`: An array of `route` structs, where each struct defines a single hop in the swap.
    -   `to`: The address of the recipient.
    -   `deadline`: The deadline for the transaction.
-   **Returns:** An array of amounts for each step of the swap.
-   **Interaction:** This function interacts with the `Pair` contracts for each hop in the route, calling the `swap` function on each one. It also handles the transfer of tokens between the pairs.
