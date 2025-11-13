# MinterUpgradeable.sol Function-Level Documentation

This document provides a detailed explanation of the functions in the `MinterUpgradeable.sol` contract.

## `update_period()`

-   **Purpose:** This is the core function of the minter. It is called once per week to mint new `THE` tokens and distribute them to the various stakeholders in the protocol.
-   **Returns:** The new active period timestamp.
-   **Interaction:**
    -   Calls `weekly_emission()` to determine the amount of `THE` to mint.
    -   Calls `calculate_rebase()` to determine the portion of the minted `THE` to be distributed to `veTHE` holders.
    -   Interacts with the `Thena` contract to mint the new `THE` tokens.
    -   Transfers the rebase amount to the `RewardsDistributor` contract.
    -   Transfers the team's share of the emissions to the team address.
    -   Transfers the remaining `THE` to the `VoterV3` contract to be distributed to the gauges.

## `circulating_supply()`

-   **Purpose:** This function calculates the circulating supply of `THE` tokens, which is used to determine the weekly emission rate.
-   **Returns:** The circulating supply of `THE` tokens.
-   **Interaction:** This function reads the total supply of `THE` from the `Thena` contract and subtracts the amount of `THE` locked in the `VotingEscrow` contract.

## `weekly_emission()`

-   **Purpose:** This function calculates the amount of `THE` to be minted in the current week. The emission rate is dynamic and depends on the circulating supply.
-   **Returns:** The weekly emission amount.
-   **Interaction:** This function calls `calculate_emission()` and `circulating_emission()` to determine the weekly emission rate.

## `calculate_rebase(uint _weeklyMint)`

-   **Purpose:** This function calculates the portion of the weekly emissions that will be distributed to `veTHE` holders as a rebase, which helps to offset the dilution from the new emissions.
-   **Parameters:**
    -   `_weeklyMint`: The total weekly mint amount.
-   **Returns:** The rebase amount.
-   **Interaction:** This function reads the total supply of `THE` and the amount of `THE` locked in the `VotingEscrow` contract to determine the rebase amount.

## `setTeam(address _team)`

-   **Purpose:** This function allows the current team to set a new team address to receive a portion of the emissions.
-   **Parameters:**
    -   `_team`: The new team address.

## `setTeamRate(uint _teamRate)`

-   **Purpose:** This function allows the team to set the percentage of emissions that will be allocated to the team.
-   **Parameters:**
    -   `_teamRate`: The new team rate in basis points (e.g., 300 for 3%).
