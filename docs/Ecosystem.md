# Thena Protocol Ecosystem Overview

## Architecture

The Thena protocol is a decentralized exchange (DEX) with a ve-tokenomics model. The core components of the protocol are:

- **Thena/veTHE:** The native token and its vote-escrowed counterpart.
- **AMM:** Automated market maker with stable and volatile pools.
- **Gauges:** Contracts that distribute `THE` emissions to liquidity providers.
- **Bribes:** A system for rewarding `veTHE` holders for voting for specific gauges.
- **Minter:** A contract that controls the minting of new `THE` tokens.

## Tokenomics

- **`THE`:** The native utility token of the protocol, used for liquidity provision and locking.
- **`veTHE`:** Vote-escrowed `THE`, which gives holders voting power and a share of protocol fees.

## Key Processes

- **Swapping:** Users can swap tokens through the AMM pools, paying a small fee that goes to liquidity providers and `veTHE` holders.
- **Liquidity Provision:** Users can provide liquidity to the AMM pools to earn trading fees and `THE` emissions.
- **Voting:** `veTHE` holders can vote on gauges to direct `THE` emissions to specific pools.
- **Bribing:** Users can offer bribes to `veTHE` holders to incentivize them to vote for certain gauges.
- **Emission Distribution:** The minter contract mints new `THE` tokens each week and distributes them to gauges based on the voting results.
