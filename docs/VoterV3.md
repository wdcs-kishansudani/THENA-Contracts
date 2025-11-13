# VoterV3.sol - In-Depth Analysis

This document provides a detailed, line-by-line analysis of the core functions in the `VoterV3.sol` contract.

## `vote(uint256 _tokenId, address[] calldata _poolVote, uint256[] calldata _weights)`

This is the central function that allows `veTHE` holders to direct the flow of `THE` emissions to their preferred liquidity pools.

```solidity
function vote(uint256 _tokenId, address[] calldata _poolVote, uint256[] calldata _weights) external nonReentrant {
    // 1. _voteDelay(_tokenId);
    //    Checks if the user has voted recently, enforcing a cooldown period between votes to prevent spamming.
    _voteDelay(_tokenId);

    // 2. require(IVotingEscrow(_ve).isApprovedOrOwner(msg.sender, _tokenId), "!approved/Owner");
    //    Ensures that the caller is the owner of the veTHE NFT or has been approved to vote on its behalf.
    require(IVotingEscrow(_ve).isApprovedOrOwner(msg.sender, _tokenId), "!approved/Owner");

    // 3. require(_poolVote.length == _weights.length, "Pool/Weights length !=");
    //    Validates that the number of pools and the number of weights provided are equal.
    require(_poolVote.length == _weights.length, "Pool/Weights length !=");

    // 4. _vote(_tokenId, _poolVote, _weights);
    //    Calls the internal `_vote` function to handle the core voting logic.
    _vote(_tokenId, _poolVote, _weights);

    // 5. lastVoted[_tokenId] = _epochTimestamp() + 1;
    //    Records the timestamp of the vote to enforce the voting delay.
    lastVoted[_tokenId] = _epochTimestamp() + 1;
}

function _vote(uint256 _tokenId, address[] memory _poolVote, uint256[] memory _weights) internal {
    // 1. _reset(_tokenId);
    //    Before applying new votes, this line calls the internal `_reset` function to clear all of the user's
    //    previous votes for the given `_tokenId`. This ensures that each call to `vote` is a fresh allocation
    //    of 100% of the NFT's voting power.
    _reset(_tokenId);

    // 2. uint256 _weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);
    //    Fetches the current voting power (weight) of the user's `veTHE` NFT from the `VotingEscrow` contract.
    uint256 _weight = IVotingEscrow(_ve).balanceOfNFT(_tokenId);

    // 3. Loop to calculate `_totalVoteWeight`
    //    This loop iterates through all the pools the user wants to vote for and sums up the weights
    //    they've assigned. This total is used to normalize the weights.
    //    ...

    // 4. Loop to apply votes
    //    This is the main loop where the votes are processed and recorded.
    for (uint256 i = 0; i < _poolCnt; i++) {
        // a. uint256 _poolWeight = _weights[i] * _weight / _totalVoteWeight;
        //    Calculates the actual voting power to be allocated to the current pool. It's a fraction of the
        //    NFT's total voting power, proportional to the weight assigned by the user.

        // b. poolVote[_tokenId].push(_pool);
        //    Records that this `_tokenId` has voted for this `_pool`.

        // c. weightsPerEpoch[_time][_pool] += _poolWeight;
        //    Adds the calculated `_poolWeight` to the total weight for the pool in the current epoch. This is
        //    the value that will be used to determine the share of `THE` emissions.

        // d. votes[_tokenId][_pool] += _poolWeight;
        //    Records the specific amount of voting power the `_tokenId` has allocated to this `_pool`.

        // e. IBribe(internal_bribes[_gauge]).deposit(uint256(_poolWeight), _tokenId);
        //    Deposits the vote into the internal and external bribe contracts, making the user eligible
        //    to claim bribe rewards.
        //    ...
    }

    // 5. if (_usedWeight > 0) IVotingEscrow(_ve).voting(_tokenId);
    //    If any votes were cast, it marks the `veTHE` NFT as having voted in the `VotingEscrow` contract.
    //    This can be used to prevent certain actions (like transferring the NFT) while it has active votes.
    if (_usedWeight > 0) IVotingEscrow(_ve).voting(_tokenId);

    // 6. totalWeightsPerEpoch[_time] += _totalWeight;
    //    Updates the total voting weight across all pools for the current epoch.
    totalWeightsPerEpoch[_time] += _totalWeight;
}
```

## `distribute(address[] memory _gauges)`

This function is responsible for distributing the weekly `THE` emissions to the gauges.

```solidity
function distribute(address[] memory _gauges) external nonReentrant {
    // 1. IMinter(minter).update_period();
    //    Calls the `update_period` function on the `Minter` contract, which triggers the minting of
    //    new `THE` tokens for the week.
    IMinter(minter).update_period();

    // 2. for (uint256 x = 0; x < _gauges.length; x++) { _distribute(_gauges[x]); }
    //    Loops through the provided list of gauges and calls the internal `_distribute` function for each one.
    for (uint256 x = 0; x < _gauges.length; x++) {
        _distribute(_gauges[x]);
    }
}

function _distribute(address _gauge) internal {
    // 1. _updateForAfterDistribution(_gauge);
    //    Calculates the amount of `THE` rewards that the gauge is entitled to for the past epoch,
    //    based on the votes it received. The result is stored in the `claimable` mapping.
    _updateForAfterDistribution(_gauge);

    // 2. uint256 _claimable = claimable[_gauge];
    //    Retrieves the calculated rewards for the gauge.
    uint256 _claimable = claimable[_gauge];

    // 3. if (_claimable > 0 && isAlive[_gauge]) { ... }
    //    Checks if there are any rewards to distribute and if the gauge is active.
    if (_claimable > 0 && isAlive[_gauge]) {
        // a. claimable[_gauge] = 0;
        //    Resets the claimable amount for the gauge.

        // b. gaugesDistributionTimestmap[_gauge] = currentTimestamp;
        //    Updates the timestamp of the last distribution for the gauge.

        // c. IGauge(_gauge).notifyRewardAmount(base, _claimable);
        //    Calls the `notifyRewardAmount` function on the `GaugeV2` contract, which sends the `THE`
        //    rewards to the gauge, making them available for liquidity providers to claim.
        IGauge(_gauge).notifyRewardAmount(base, _claimable);
    }
}
```
