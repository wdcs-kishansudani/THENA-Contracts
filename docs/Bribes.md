# Bribes.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `Bribes.sol` contract.

## `deposit(uint256 amount, uint256 tokenId)`

-   **Purpose:** This function is called by the `VoterV3` contract when a user votes for a gauge. It records the user's vote, which makes them eligible to receive bribe rewards.
-   **Parameters:**
    -   `amount`: The amount of voting power the user has allocated to the gauge.
    -   `tokenId`: The ID of the `veTHE` NFT used to vote.
-   **Interaction:** This function is called by the `VoterV3` contract.

## `withdraw(uint256 amount, uint256 tokenId)`

-   **Purpose:** This function is called by the `VoterV3` contract when a user resets their votes. It removes the user's vote from the bribe contract.
-   **Parameters:**
    -   `amount`: The amount of voting power to withdraw.
    -   `tokenId`: The ID of the `veTHE` NFT.
-   **Interaction:** This function is called by the `VoterV3` contract.

## `getReward(uint256 tokenId, address[] memory tokens)`

-   **Purpose:** This function allows a user to claim their earned bribe rewards for a specific `veTHE` NFT.
-   **Parameters:**
    -   `tokenId`: The ID of the `veTHE` NFT.
    -   `tokens`: An array of token addresses to claim rewards for.
-   **Interaction:** This function calculates the user's earned rewards and transfers them from the bribe contract to the user.

## `notifyRewardAmount(address _rewardsToken, uint256 reward)`

-   **Purpose:** This function is called by users or protocols who want to add bribes to a gauge.
-   **Parameters:**
    -   `_rewardsToken`: The address of the token being offered as a bribe.
    -   `reward`: The amount of the bribe.
-   **Interaction:** This function transfers the bribe tokens from the user to the bribe contract.
