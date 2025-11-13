```mermaid
gantt
    title Thena Weekly Cycle
    dateFormat  X
    axisFormat  %s

    section Voting & Bribes
    Vote on Gauges      : 0, 604800
    Add Bribes          : 0, 604800

    section Emissions
    Minter `update_period` : 604800, 86400
    Distribution to Gauges : 691200, 86400

    section Rewards
    Claim THE Rewards   : 777600, 604800
    Claim Bribe Rewards : 777600, 604800
```
