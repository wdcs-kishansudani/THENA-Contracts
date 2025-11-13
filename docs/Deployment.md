# Deployment Guide

This guide provides instructions for deploying the Thena protocol.

## Prerequisites

-   Node.js and npm installed. You can download them from [https://nodejs.org/](https://nodejs.org/).
-   A wallet with sufficient funds to deploy the contracts.
-   An RPC URL for the network you want to deploy to. You can get one from a service like Infura or Alchemy.

## Configuration

1.  **Environment Variables:** Create a `.env` file in the root of the project and add the following environment variables:
    ```
    PRIVATE_KEY=<your-private-key>
    API_KEY=<your-api-key>
    ```
    Replace `<your-private-key>` with the private key of your wallet, and `<your-api-key>` with your API key from a service like BscScan.

2.  **Hardhat Configuration:** Open `hardhat.config.js` and configure the network you want to deploy to. You will need to add a new network configuration with the RPC URL and your private key.

## Deployment

To deploy the protocol, run the following command:

```bash
npx hardhat run scripts/V2/deploy.js --network <network-name>
```

Replace `<network-name>` with the name of the network you configured in `hardhat.config.js` (e.g., `mainnet`, `testnet`).

### Post-Deployment Steps

After deploying the contracts, you will need to perform the following steps to initialize the protocol:

1.  **Set Minter:** Call the `setMinter` function on the `Thena` contract to transfer minting rights to the `MinterUpgradeable` contract.
    ```javascript
    const thena = await ethers.getContract("Thena");
    const minter = await ethers.getContract("MinterUpgradeable");
    await thena.setMinter(minter.address);
    ```

2.  **Initialize Voter:** Call the `_init` function on the `VoterV3` contract to whitelist initial tokens and set the minter and permissions registry.
    ```javascript
    const voter = await ethers.getContract("VoterV3");
    const minter = await ethers.getContract("MinterUpgradeable");
    const permissionsRegistry = await ethers.getContract("PermissionsRegistry");
    const initialTokens = ["<token1-address>", "<token2-address>"];
    await voter._init(initialTokens, permissionsRegistry.address, minter.address);
    ```

3.  **Initialize Minter:** Call the `_initialize` function on the `MinterUpgradeable` contract to set up initial veTHE locks. This is optional and only needs to be done if you want to create initial locks for specific users.
    ```javascript
    const minter = await ethers.getContract("MinterUpgradeable");
    const claimants = ["<user1-address>", "<user2-address>"];
    const amounts = ["<amount1>", "<amount2>"];
    const max = "<max-amount>";
    await minter._initialize(claimants, amounts, max);
    ```
