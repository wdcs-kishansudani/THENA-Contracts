```mermaid
graph TD
    subgraph Swapping
        A[User with Token A] -->|1. SwapExactTokensForTokens| B(RouterV2);
        B -->|2. Calls Pair.swap| C{Pair (Token A / Token B)};
        C -->|3. Sends Token B| A;
    end

    subgraph Liquidity Provision
        D[User with Token A & B] -->|1. AddLiquidity| E(RouterV2);
        E -->|2. Transfers Tokens & Calls Pair.mint| F{Pair (Token A / Token B)};
        F -->|3. Mints LP Tokens| D;
    end

    subgraph Staking & Earning
        G[User with LP Tokens] -->|1. Deposit LP Tokens| H(GaugeV2);
        H -->|2. Earns THE emissions| G;
        G -->|3. GetReward| H;
        H -->|4. Sends THE rewards| G;
    end

    subgraph Governance
        I[User with THE] -->|1. CreateLock| J(VotingEscrow);
        J -->|2. Mints veTHE NFT| I;
        I -->|3. Vote on Gauges with veTHE| K(VoterV3);
        K -->|4. Influences Minter emissions| L(MinterUpgradeable);
        L -->|5. Distributes THE to Gauges| H;
    end

    subgraph Bribes
        M[External Protocol / User] -->|1. Adds bribe to a Gauge| N(Bribes);
        I -->|2. Votes on Gauge| K;
        K -->|3. Records vote in Bribe contract| N;
        I -->|4. Claim Bribes| N;
        N -->|5. Sends bribe rewards| I;
    end
```
