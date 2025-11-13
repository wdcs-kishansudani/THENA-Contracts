# VotingEscrow.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `VotingEscrow.sol` contract.

## `create_lock(uint _value, uint _lock_duration)`

-   **Purpose:** This function allows a user to lock their `THE` tokens in exchange for a `veTHE` NFT. The voting power of the NFT is proportional to the amount of `THE` locked and the duration of the lock.
-   **Parameters:**
    -   `_value`: The amount of `THE` tokens to lock.
    -   `_lock_duration`: The duration of the lock in seconds. The maximum lock duration is 2 years.
-   **Returns:** The ID of the newly created `veTHE` NFT.
-   **Interaction:** This function interacts with the `Thena` contract to transfer the `THE` tokens to the `VotingEscrow` contract. It also mints a new `veTHE` NFT.

## `increase_amount(uint _tokenId, uint _value)`

-   **Purpose:** This function allows a user to add more `THE` tokens to an existing lock, thereby increasing the voting power of their `veTHE` NFT.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
    -   `_value`: The amount of `THE` tokens to add to the lock.
-   **Interaction:** This function interacts with the `Thena` contract to transfer the `THE` tokens to the `VotingEscrow` contract.

## `increase_unlock_time(uint _tokenId, uint _lock_duration)`

-   **Purpose:** This function allows a user to extend the lock duration of their `veTHE` NFT, which also increases its voting power.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
    -   `_lock_duration`: The new lock duration in seconds.
-   **Interaction:** This function does not involve any token transfers, but it does update the lock's end time, which affects the voting power calculation.

## `withdraw(uint _tokenId)`

-   **Purpose:** This function allows a user to withdraw their locked `THE` tokens after the lock has expired. The `veTHE` NFT is burned in the process.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Interaction:** This function interacts with the `Thena` contract to transfer the `THE` tokens back to the user. It also burns the `veTHE` NFT.

## `balanceOfNFT(uint _tokenId)`

-   **Purpose:** This function calculates the current voting power of a `veTHE` NFT. The voting power decays linearly over time.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Returns:** The current voting power of the NFT.
-   **Interaction:** This function is called by the `VoterV3` contract to determine the weight of a user's vote.

## `ownerOf(uint _tokenId)`

-   **Purpose:** This is a standard ERC721 function that returns the owner of a given `veTHE` NFT.
-   **Parameters:**
    -   `_tokenId`: The ID of the `veTHE` NFT.
-   **Returns:** The address of the owner.

## `merge(uint _from, uint _to)`

-   **Purpose:** This function allows a user to merge two `veTHE` NFTs into a single NFT. This can be useful for consolidating voting power.
-   **Parameters:**
    -   `_from`: The ID of the `veTHE` NFT to merge from.
    -   `_to`: The ID of the `veTHE` NFT to merge into.
-   **Interaction:** This function burns the `_from` NFT and transfers its locked `THE` tokens to the `_to` NFT.

## `split(uint[] memory amounts, uint _tokenId)`

-   **Purpose:** This function allows a user to split a `veTHE` NFT into multiple new NFTs. This can be useful for diversifying voting power or for selling a portion of a lock.
-   **Parameters:**
    -   `amounts`: An array of percentages to split the NFT by.
    -   `_tokenId`: The ID of the `veTHE` NFT to split.
-   **Interaction:** This function burns the original NFT and mints new NFTs with the specified locked amounts.
