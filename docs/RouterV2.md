# RouterV2.sol - In-Depth Analysis

This document provides a detailed, line-by-line analysis of the core functions in the `RouterV2.sol` contract.

## `addLiquidity(address tokenA, address tokenB, bool stable, ...)`

This function is the main entry point for users to add liquidity to a pool.

```solidity
function addLiquidity(
    address tokenA, address tokenB, bool stable,
    uint amountADesired, uint amountBDesired,
    uint amountAMin, uint amountBMin,
    address to, uint deadline
) external ensure(deadline) returns (uint amountA, uint amountB, uint liquidity) {
    // 1. (amountA, amountB) = _addLiquidity(...);
    //    Calls the internal `_addLiquidity` function to calculate the optimal amounts of tokens
    //    to deposit, based on the current reserves of the pool. This prevents users from adding
    //    liquidity at an unfavorable price.
    (amountA, amountB) = _addLiquidity(tokenA, tokenB, stable, amountADesired, amountBDesired, amountAMin, amountBMin);

    // 2. address pair = pairFor(tokenA, tokenB, stable);
    //    Calculates the deterministic address of the pair contract.
    address pair = pairFor(tokenA, tokenB, stable);

    // 3. _safeTransferFrom(tokenA, msg.sender, pair, amountA); ...
    //    Transfers the calculated amounts of tokens from the user to the pair contract.
    _safeTransferFrom(tokenA, msg.sender, pair, amountA);
    _safeTransferFrom(tokenB, msg.sender, pair, amountB);

    // 4. liquidity = IBaseV1Pair(pair).mint(to);
    //    Calls the `mint` function on the pair contract, which mints new LP tokens and sends
    //    them to the specified recipient (`to`).
    liquidity = IBaseV1Pair(pair).mint(to);
}
```

## `removeLiquidity(address tokenA, address tokenB, bool stable, ...)`

This function is the main entry point for users to remove liquidity from a pool.

```solidity
function removeLiquidity(
    address tokenA, address tokenB, bool stable,
    uint liquidity, uint amountAMin, uint amountBMin,
    address to, uint deadline
) public ensure(deadline) returns (uint amountA, uint amountB) {
    // 1. address pair = pairFor(tokenA, tokenB, stable);
    //    Calculates the deterministic address of the pair contract.
    address pair = pairFor(tokenA, tokenB, stable);

    // 2. require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity));
    //    Transfers the user's LP tokens to the pair contract.
    require(IBaseV1Pair(pair).transferFrom(msg.sender, pair, liquidity));

    // 3. (uint amount0, uint amount1) = IBaseV1Pair(pair).burn(to);
    //    Calls the `burn` function on the pair contract, which burns the LP tokens and returns
    //    the underlying tokens to the specified recipient (`to`).
    (uint amount0, uint amount1) = IBaseV1Pair(pair).burn(to);

    // 4. (amountA, amountB) = tokenA == token0 ? (amount0, amount1) : (amount1, amount0);
    //    Unsorts the token amounts to match the order of the tokens passed in as arguments.
    (address token0,) = sortTokens(tokenA, tokenB);
    (amountA, amountB) = tokenA == token0 ? (amount0, amount1) : (amount1, amount0);

    // 5. require(amountA >= amountAMin, '...');
    //    Checks that the amounts of tokens received are greater than or equal to the minimum
    //    amounts specified by the user, protecting against slippage.
    require(amountA >= amountAMin, 'BaseV1Router: INSUFFICIENT_A_AMOUNT');
    require(amountB >= amountBMin, 'BaseV1Router: INSUFFICIENT_B_AMOUNT');
}
```

## `swapExactTokensForTokens(uint amountIn, uint amountOutMin, route[] calldata routes, ...)`

This function allows users to perform a multi-hop swap with a precise input amount.

```solidity
function swapExactTokensForTokens(
    uint amountIn, uint amountOutMin,
    route[] calldata routes,
    address to, uint deadline
) external ensure(deadline) returns (uint[] memory amounts) {
    // 1. amounts = getAmountsOut(amountIn, routes);
    //    Calculates the expected output amount for each hop in the swap route.
    amounts = getAmountsOut(amountIn, routes);

    // 2. require(amounts[amounts.length - 1] >= amountOutMin, '...');
    //    Ensures that the final output amount is greater than or equal to the minimum amount
    //    specified by the user.
    require(amounts[amounts.length - 1] >= amountOutMin, 'BaseV1Router: INSUFFICIENT_OUTPUT_AMOUNT');

    // 3. _safeTransferFrom(routes[0].from, msg.sender, pairFor(...), amounts[0]);
    //    Transfers the input tokens from the user to the first pair in the route.
    _safeTransferFrom(
        routes[0].from, msg.sender, pairFor(routes[0].from, routes[0].to, routes[0].stable), amounts[0]
    );

    // 4. _swap(amounts, routes, to);
    //    Calls the internal `_swap` function to execute the series of swaps between the pairs.
    _swap(amounts, routes, to);
}
```
