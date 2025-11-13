# VoterV3.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `VoterV3.sol` contract.

## `createGauge(address _pool, uint256 _gaugeType)`

-   **Purpose:** This function allows anyone to create a new gauge for a specific liquidity pool. Gauges are responsible for distributing `THE` emissions to liquidity providers.
-   **Parameters:**
    -   `_pool`: The address of the liquidity pool.
    -   `_gaugeType`: The type of gauge to create (e.g., for a stable or volatile pool).
-   **Returns:** The address of the newly created gauge, its internal bribe contract, and its external bribe contract.
-   **Interaction:** This function interacts with a `GaugeFactory` to create the gauge and a `BribeFactory` to create the associated bribe contracts. It also requires that the tokens in the pool are whitelisted.

## `vote(uint256 _tokenId, address[] calldata _poolVote, uint256[] calldata _weights)`

-   **Purpose:** This is the core function for gauge voting. It allows `veTHE` holders to allocate their voting power to different gauges, thereby influencing the distribution of `THE` emissions.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT to vote with.
    -   `_poolVote`: An array of pool addresses to vote for.
    -   `_weights`: An array of weights (in basis points) corresponding to the pools. The total weight must sum to 10,000.
-   **Interaction:** This function interacts with the `VotingEscrow` contract to get the voting power of the NFT. It also interacts with the `Bribes` contracts to deposit the user's vote, making them eligible for bribe rewards.

## `claimBribes(address[] memory _bribes, address[][] memory _tokens, uint256 _tokenId)`

-   **Purpose:** This function allows `veTHE` holders to claim the bribe rewards they have earned by voting for certain gauges.
-   **Parameters:**
    -   `_bribes`: An array of bribe contract addresses to claim from.
    -   `_tokens`: A 2D array of token addresses to claim rewards for.
    -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Interaction:** This function interacts with the `Bribes` contracts to withdraw the user's earned rewards.

## `distribute(address[] memory _gauges)`

-   **Purpose:** This function distributes `THE` emissions to a list of gauges. It is typically called once per epoch (week).
-   **Parameters:**
    -   `_gauges`: An array of gauge addresses to distribute emissions to.
-   **Interaction:** This function is called by the `MinterUpgradeable` contract, which provides the `THE` tokens to be distributed. It then calls the `notifyRewardAmount` function on each gauge to send the rewards.

## `reset(uint256 _tokenId)`

-   **Purpose:** This function allows a `veTHE` holder to reset their votes, effectively removing their voting power from all gauges they have voted for.
-   -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Interaction:** This function interacts with the `Bribes` contracts to withdraw the user's vote.

## `poke(uint256 _tokenId)`

-   **Purpose:** This function allows a `veTHE` holder to re-cast their votes with their current voting power. This is useful if they have increased their lock time or amount, as their voting power will have increased.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Interaction:** This function is similar to `vote`, but it uses the user's existing vote distribution.
