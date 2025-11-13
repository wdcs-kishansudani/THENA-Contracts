# PairFactory.sol and Pair.sol - In-Depth Analysis

This document provides a detailed, line-by-line analysis of the core functions in the `PairFactory.sol` and `Pair.sol` contracts.

## `PairFactory.sol`

### `createPair(address tokenA, address tokenB, bool stable)`

This function is the sole entry point for creating new liquidity pools.

```solidity
function createPair(address tokenA, address tokenB, bool stable) external returns (address pair) {
    // 1. require(tokenA != tokenB, 'IA');
    //    Ensures that the two tokens are not the same.
    require(tokenA != tokenB, 'IA');

    // 2. (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);
    //    Sorts the tokens by address to ensure that the pair is always created with the same
    //    token order, regardless of the order in which they were passed in.
    (address token0, address token1) = tokenA < tokenB ? (tokenA, tokenB) : (tokenB, tokenA);

    // 3. require(getPair[token0][token1][stable] == address(0), 'PE');
    //    Checks if a pair for this combination of tokens and stability type already exists.
    require(getPair[token0][token1][stable] == address(0), 'PE');

    // 4. bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable));
    //    Creates a unique salt for the CREATE2 opcode by hashing the token addresses and the
    //    stability type. This allows for deterministic pair addresses.
    bytes32 salt = keccak256(abi.encodePacked(token0, token1, stable));

    // 5. (_temp0, _temp1, _temp) = (token0, token1, stable);
    //    Stores the token addresses and stability type in temporary state variables so that they
    //    can be accessed by the `Pair` contract's constructor.
    (_temp0, _temp1, _temp) = (token0, token1, stable);

    // 6. pair = address(new Pair{salt:salt}());
    //    Deploys a new `Pair` contract using the CREATE2 opcode with the generated salt.
    pair = address(new Pair{salt:salt}());

    // 7. getPair[token0][token1][stable] = pair; ...
    //    Stores the address of the new pair in the `getPair` mapping for future lookups.
    getPair[token0][token1][stable] = pair;
    getPair[token1][token0][stable] = pair;

    // 8. allPairs.push(pair);
    //    Adds the new pair to the list of all pairs.
    allPairs.push(pair);

    // 9. isPair[pair] = true;
    //    Marks the new address as a valid pair.
    isPair[pair] = true;

    // 10. emit PairCreated(...);
    //     Emits an event to log the creation of the new pair.
    emit PairCreated(token0, token1, stable, pair, allPairs.length);
}
```

## `Pair.sol`

### `swap(uint amount0Out, uint amount1Out, address to, bytes calldata data)`

This function is the heart of the AMM, executing token swaps.

```solidity
function swap(uint amount0Out, uint amount1Out, address to, bytes calldata data) external lock {
    // 1. require(!PairFactory(factory).isPaused());
    //    Checks if the factory is paused. If it is, swaps are disabled.
    require(!PairFactory(factory).isPaused());

    // 2. require(amount0Out > 0 || amount1Out > 0, 'IOA');
    //    Ensures that at least one of the output amounts is greater than zero.
    require(amount0Out > 0 || amount1Out > 0, 'IOA');

    // 3. (uint _reserve0, uint _reserve1) = (reserve0, reserve1);
    //    Loads the current reserves into memory.
    (uint _reserve0, uint _reserve1) =  (reserve0, reserve1);

    // 4. require(amount0Out < _reserve0 && amount1Out < _reserve1, 'IL');
    //    Checks that the requested output amounts are less than the current reserves.
    require(amount0Out < _reserve0 && amount1Out < _reserve1, 'IL');

    // 5. if (amount0Out > 0) _safeTransfer(_token0, to, amount0Out); ...
    //    Optimistically transfers the requested output tokens to the recipient (`to`).
    if (amount0Out > 0) _safeTransfer(token0, to, amount0Out);
    if (amount1Out > 0) _safeTransfer(token1, to, amount1Out);

    // 6. if (data.length > 0) IPairCallee(to).hook(...);
    //    If the `data` parameter is not empty, it calls the `hook` function on the recipient
    //    contract. This is used for flash loans.
    if (data.length > 0) IPairCallee(to).hook(msg.sender, amount0Out, amount1Out, data);

    // 7. _balance0 = IERC20(_token0).balanceOf(address(this)); ...
    //    Gets the current balances of the tokens in the pair contract.
    uint _balance0 = IERC20(token0).balanceOf(address(this));
    uint _balance1 = IERC20(token1).balanceOf(address(this));

    // 8. uint amount0In = _balance0 > _reserve0 - amount0Out ? ...
    //    Calculates the amount of input tokens that were sent to the pair contract.
    uint amount0In = _balance0 > _reserve0 - amount0Out ? _balance0 - (_reserve0 - amount0Out) : 0;
    uint amount1In = _balance1 > _reserve1 - amount1Out ? _balance1 - (_reserve1 - amount1Out) : 0;

    // 9. require(amount0In > 0 || amount1In > 0, 'IIA');
    //    Ensures that at least one of the input amounts is greater than zero.
    require(amount0In > 0 || amount1In > 0, 'IIA');

    // 10. if (amount0In > 0) _update0(amount0In * PairFactory(factory).getFee(stable) / 10000); ...
    //     Calculates the trading fees and sends them to the `PairFees` contract.
    if (amount0In > 0) _update0(amount0In * PairFactory(factory).getFee(stable) / 10000);
    if (amount1In > 0) _update1(amount1In * PairFactory(factory).getFee(stable) / 10000);

    // 11. require(_k(_balance0, _balance1) >= _k(_reserve0, _reserve1), 'K');
    //     Checks that the constant product formula (k) is satisfied. This is the core of the AMM.
    require(_k(_balance0, _balance1) >= _k(_reserve0, _reserve1), 'K');

    // 12. _update(_balance0, _balance1, _reserve0, _reserve1);
    //     Updates the reserves and the TWAP oracle.
    _update(_balance0, _balance1, _reserve0, _reserve1);

    // 13. emit Swap(...);
    //     Emits an event to log the swap.
    emit Swap(msg.sender, amount0In, amount1In, amount0Out, amount1Out, to);
}
```
