# MinterUpgradeable.sol - In-Depth Analysis

This document provides a detailed, line-by-line analysis of the core functions in the `MinterUpgradeable.sol` contract.

## `update_period()`

This is the most critical function in the `MinterUpgradeable` contract. It is responsible for minting and distributing the weekly `THE` emissions.

```solidity
function update_period() external returns (uint) {
    // 1. if (block.timestamp >= active_period + WEEK && _initializer == address(0))
    //    This check ensures that the function can only be called once per week, after the current
    //    `active_period` has ended. It also checks that the `_initializer` address is zero, which
    //    means that the initial setup of the minter is complete.
    if (block.timestamp >= active_period + WEEK && _initializer == address(0)) {
        // a. active_period = (block.timestamp / WEEK) * WEEK;
        //    Updates the `active_period` to the beginning of the current week.

        // b. if(!isFirstMint){ weekly = weekly_emission(); } else { isFirstMint = false; }
        //    Calculates the weekly emission amount. For the very first mint, it uses the initial
        //    hardcoded value. For all subsequent mints, it calls the `weekly_emission()` function
        //    to dynamically determine the emission rate.

        // c. uint _rebase = calculate_rebase(weekly);
        //    Calculates the portion of the weekly emissions that will be distributed to `veTHE`
        //    holders as a rebase.

        // d. uint _teamEmissions = weekly * teamRate / PRECISION;
        //    Calculates the portion of the weekly emissions that will be distributed to the team.

        // e. uint _gauge = weekly - _rebase - _teamEmissions;
        //    Calculates the remaining portion of the weekly emissions that will be distributed
        //    to the gauges.

        // f. if (_thena.balanceOf(address(this)) < weekly) { _thena.mint(address(this), weekly - _thena.balanceOf(address(this))); }
        //    Mints the required amount of `THE` tokens to the `MinterUpgradeable` contract.

        // g. require(_thena.transfer(team, _teamEmissions));
        //    Transfers the team's share of the emissions to the team address.

        // h. require(_thena.transfer(address(_rewards_distributor), _rebase));
        //    Transfers the rebase amount to the `RewardsDistributor` contract.

        // i. _rewards_distributor.checkpoint_token(); ...
        //    Calls the `checkpoint_token` and `checkpoint_total_supply` functions on the
        //    `RewardsDistributor` contract to update its internal state.

        // j. _thena.approve(address(_voter), _gauge);
        //    Approves the `VoterV3` contract to spend the `THE` tokens allocated for the gauges.

        // k. _voter.notifyRewardAmount(_gauge);
        //    Calls the `notifyRewardAmount` function on the `VoterV3` contract, which begins
        //    the process of distributing the `THE` tokens to the gauges.

        // ...
    }
    // ...
}
```

## `calculate_rebase(uint _weeklyMint)`

This function determines the portion of weekly emissions that are distributed to `veTHE` holders to protect them from dilution.

```solidity
function calculate_rebase(uint _weeklyMint) public view returns (uint) {
    // 1. uint _veTotal = _thena.balanceOf(address(_ve));
    //    Gets the total amount of THE locked in the VotingEscrow contract.
    uint _veTotal = _thena.balanceOf(address(_ve));

    // 2. uint _thenaTotal = _thena.totalSupply();
    //    Gets the total supply of THE tokens.
    uint _thenaTotal = _thena.totalSupply();

    // 3. uint lockedShare = (_veTotal) * PRECISION / _thenaTotal;
    //    Calculates the percentage of the total THE supply that is locked.
    uint lockedShare = (_veTotal) * PRECISION  / _thenaTotal;

    // 4. if(lockedShare >= REBASEMAX){ ... } else { ... }
    //    This logic caps the rebase at a maximum percentage (`REBASEMAX`). If the share of locked
    //    THE is greater than or equal to the max, the rebase is calculated based on the max.
    //    Otherwise, it's calculated based on the actual locked share. This prevents an excessive
    //    amount of emissions from going to veTHE holders, ensuring that there are sufficient
    //    rewards for liquidity providers.
    if(lockedShare >= REBASEMAX){
        return _weeklyMint * REBASEMAX / PRECISION;
    } else {
        return _weeklyMint * lockedShare / PRECISION;
    }
}
```
