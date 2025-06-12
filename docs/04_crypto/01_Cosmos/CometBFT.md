---
title: CometBFT
slug: cometBFT
date: 2025-06-12 14:06:06
update: 2025-06-12 14:06:06
publish: true
---

> [!Note] Tendermint core = Comet BFT
> Tendermint core is just a different name of Comet BFT

[CometBFT](https://github.com/cometbft/cometbft) is a Byzantine Fault Tolerant consensus engine. designed for blockchain systems. It handles:
1. Networking: p2p communication between nodes
2. Consensus: agreeing on a block among potentially faulty or malicious nodes
3. Application interface via ABCI

### Key Properties

| Property      | Description                                                                      |
| ------------- | -------------------------------------------------------------------------------- |
| BFT Safety    | Can tolerate up to 1/3 of validators being faulty or malicious.                  |
| Liveness      | As long as >2/3 validators are online and honest, it will make progress          |
| Fast Finality | Once a block is committed, it's final - no forks                                 |
| ABCI Support  | Your application is abstracted via ABCI - you can even write it in Go, Rust, etc |

### Lifecycle of a Block in Comet BFT
1. **Txs arrive** -> mempool collects them.
2. **Proposal phase** -> proposer node suggest a block.
3. **Prevote / Precommit** (BFT consensus rounds).
4. **Commit** -> all nodes agree and finalize block.
5. **[[ABCI]] Commit** -> passes the block to the application for state update.

