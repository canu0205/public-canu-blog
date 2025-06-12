---
title: ABCI
slug: abci
date: 2025-06-12 14:06:06
update: 2025-06-12 14:06:06
publish: true
---
**Application Blockchain Interface(ABCI)** is an interface that defines the boundary between the replication engine(blockchain), and the state machine(application). Using a socket protocol(as v0.38.x, it supports gRPC), a consensus engine running in one process can manage an application state running in another.

https://github.com/cometbft/cometbft/tree/v1.0.1/abci

With this repository, developers can build their own blockchains(or applications) in any language that supports sockets since the ABCI is a socket protocol.

Below is super simple Counter using ABCI that i made.
https://github.com/canu0205/cosmos_hands_on_ex/tree/master/abci-counter

## Key Functions in ABCI
[abci github spec](https://github.com/cometbft/cometbft/blob/v0.37.x/spec/abci/abci++_basic_concepts.md#consensusblock-execution-methods)
[abci tutorial](https://tutorials.cosmos.network/academy/2-cosmos-concepts/1-architecture.html#test-yourself-a-pseudo-coding-exercise)
### `CheckTx`
Many functions that could be broadcast should not be broadcast.
Exwamples include malformed transactions and spam-like artifacts. However, CometBFT cannot determine the transaction interpretation because it is agnostic to it. To address this, the ABCI includes `CheckTx` method. CometBFT uses this method to ask the application layer if a transaction is valid. Applications implement this function. After the txs pass this function, they are added to a mempool.
### `DeliverTx`
CometBFT calls the `DeliverTx` method to pass block information to the application layer for interpretation and possible state machine transition.
### `Commit`
Instructs the Application to persist its state. It is a fundamental part of CometBFT's crash-recovery mechanism that ensures the synchronization between CometBFT and the Application upon recovery. CometBFT calls it just after having persisted the data returned by calls to `DeliverTx` and `EndBlock` . The Application can now discard any state or data except the one resulting from executing the transactions in the decided block.
### `BeginBlock` and `EndBlock`
`BeginBlock` and `EndBlock` messages are sent through the ABCI even if blocks contain no transactions. This provides positive confirmation of basic connectivity and helps identify time periods with no operations. These methods facilitate the execution of scheduled processes that should always run because they call methods at the application level, where developers define processes.

![[Screenshot 2025-05-16 at 4.01.06 PM.png]]
Only communicates at the **`FinalizeCommit` Phase**.