# Testing Guide

This guide provides instructions for running the test suite for the Thena protocol. The tests are written using Hardhat and Chai, and they cover the core functionality of the protocol.

## Prerequisites

-   Node.js and npm installed. You can download them from [https://nodejs.org/](https://nodejs.org/).
-   A local development environment set up. You can use Hardhat, which is the recommended development environment for this project.

## Installation

1.  Clone the repository:
    ```bash
    git clone <repository-url>
    ```
2.  Install the dependencies:
    ```bash
    npm install
    ```

## Running the Tests

To run the entire test suite, use the following command:

```bash
npx hardhat test
```

This will execute all the tests in the `test` directory.

### Running a Specific Test File

You can also run a specific test file by providing the path to the file:

```bash
npx hardhat test test/testCL.js
```

### Test Coverage

The test suite covers the following core contracts:

-   `Thena.sol`
-   `VotingEscrow.sol`
-   `VoterV3.sol`
-   `MinterUpgradeable.sol`
-   `PairFactory.sol`
-   `Pair.sol`
-   `RouterV2.sol`
-   `GaugeV2.sol`
-   `Bribes.sol`

The tests are designed to ensure that the contracts are functioning correctly and that they are secure.
