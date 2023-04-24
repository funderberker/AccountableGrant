# Accountable Grants

## Problem
Currently Nouns DAO does not have a good system for facilitating large payments for proposals with long timelines or uncertain deliverables. Large payments upfront lack ability to ensure completion of tasks and existing escrow contracts require additional transactions or a non-DAO third party, which undermines the intent of the DAO governance system.

## Solution
Rather than paying a grant as a single upfront lump sum payment, the Accountable Grant smart contract will unlock payout in 3 different phases:
1. Initial payment
2. Linear vesting
3. Completion payment

If the funded project does not succeed or the proposer abandons the project the DAO will be able to launch a follow up vote to cancel the grant and return all unclaimed funds.

## Implementation Details
These smart contracts will facilitate DAO native escrow of payments that are configurable to the needs of the proposal without sacrificing DAO control. The escrow would be optimistically unlocked unless the DAO were to pass a follow up proposal to cancel payments, at which time all unclaimed funds would be returned to the DAO. In the best case there will be no additional steps for the DAO to perform - one transaction to create and fund an escrow contract. In the worst case the DAO would need to perform a second transaction, via standard proposal system, to cancel an engagement. Optimistic escrow functionality would be useable for Nouns proposals, small grants, and all Nouns derivative protocols.

### Configurable parameters
- **Funding**: Total ETH to be funded
- **Initial Funds**: Amount of ETH accessible immediately
- **Vesting Funds**: Amount of ETH to linearly unlock
- **Completion Funds**: Amount of funds accessible after completion time + validation delay
- **Completion Timestamp**: Expected completion timestamp, used for vesting rate.
- **Validation Delay**: Seconds beyond completion time before completion funds unlock. Allows opportunity for DAO to decide and vote if and only if work not finished

### Functions
- **Create**: Send funds and create a new instance of escrow contract.
- **EarlyComplete**: Immediately unlock all funds for payee.
- **Cancel**: Terminate the grant and return all unclaimed funds to the original funder.
- **Delay Completion**: Push back the completion time without rescinding funds.

### Technical details:
Escrow contract will be implemented using a Minimal Proxy Clone system. This will allow shared logic to be implemented as a single contract that all DAOs can use for all proposals. Each proposal will be spawned as a new clone which holds the assets, owner address, and payee address. Payees are free to withdraw any unlocked funds at any time by calling functions against the clone address that they are paid by. The contract will be non upgradeable.

### Additional notes:
This contract will be designed to be DAO native and factor in the specific needs of the Nouns governance system. However, it will live on chain and can be used by any Nouns derivatives or even non-nounish DAOs.
