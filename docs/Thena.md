# Thena.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `Thena.sol` contract.

## `setMinter(address _minter)`

-   **Purpose:** This function allows the current minter to transfer the minting authority to a new address. This is a critical function for establishing the token's monetary policy, as the minter is the only entity that can create new `THE` tokens.
-   **Parameters:**
    -   `_minter`: The address of the new minter.
-   **Usage:** This function is intended to be called only once at the time of deployment to set the `MinterUpgradeable` contract as the sole minter of `THE` tokens.
-   **Interaction:** This function directly impacts the `mint` function, as only the address set here will be able to call it.

## `initialMint(address _recipient)`

-   **Purpose:** This function is responsible for minting the initial supply of 50 million `THE` tokens. This is a one-time event that establishes the initial token distribution.
-   **Parameters:**
    -   `_recipient`: The address that will receive the initial 50 million `THE` tokens.
-   **Usage:** This function must be called by the minter before any other minting operations can occur. It can only be called once.
-   **Interaction:** This function calls the internal `_mint` function to create the initial supply of `THE` tokens.

## `approve(address _spender, uint _value)`

-   **Purpose:** This is a standard ERC20 function that allows a token holder to approve another address (the spender) to withdraw a certain amount of tokens from their account.
-   **Parameters:**
    -   `_spender`: The address of the account that will be approved to spend the tokens.
    -   `_value`: The maximum amount of tokens the spender is approved to withdraw.
-   **Returns:** A boolean value indicating whether the approval was successful.
-   **Interaction:** This function is used in conjunction with `transferFrom` to allow other contracts (like the `RouterV2` or `VotingEscrow`) to move `THE` tokens on behalf of the user.

## `mint(address account, uint amount)`

-   **Purpose:** This function allows the minter to create new `THE` tokens and assign them to a specific account. This is the primary mechanism for increasing the total supply of `THE` tokens.
-   **Parameters:**
    -   `account`: The address that will receive the newly minted tokens.
    -   `amount`: The amount of `THE` tokens to mint.
-   **Returns:** A boolean value indicating whether the minting was successful.
-   **Interaction:** This function can only be called by the current minter address, which is set by the `setMinter` function. It calls the internal `_mint` function to update the total supply and the recipient's balance.

## `transfer(address _to, uint _value)`

-   **Purpose:** This is a standard ERC20 function that allows a token holder to transfer `THE` tokens to another address.
-   **Parameters:**
    -   `_to`: The address of the recipient.
    -   `_value`: The amount of `THE` tokens to transfer.
-   **Returns:** A boolean value indicating whether the transfer was successful.
-   **Interaction:** This function calls the internal `_transfer` function to update the balances of the sender and recipient.

## `transferFrom(address _from, address _to, uint _value)`

-   **Purpose:** This is a standard ERC20 function that allows a spender to transfer `THE` tokens from one address to another, provided the spender has been approved to do so.
-   **Parameters:**
    -   `_from`: The address of the sender.
    -   `_to`: The address of the recipient.
    -   `_value`: The amount of `THE` tokens to transfer.
-   **Returns:** A boolean value indicating whether the transfer was successful.
-   **Interaction:** This function relies on the `approve` function to have been called previously by the `_from` address. It calls the internal `_transfer` function to update the balances of the sender and recipient.
