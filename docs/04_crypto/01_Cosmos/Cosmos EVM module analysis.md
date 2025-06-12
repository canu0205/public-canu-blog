---
title: Cosmos EVM module analysis
slug: cosmos-evm-analysis
date: 2025-06-12 13:57:00
update: 2025-06-12 13:57:00
publish: true
---
# Tx lifecycle
## 1. Ethereum JSON-RPC phase
- User sends an Ethereum transaction via `eth_sendRawTransaction`  in `rpc/namespaces/ethereum/eth/api.go`.
- The tx converted into a cosmos-compatible tx in `rpc/backend/call_tx.go`
## 2. Cosmos layer: Transaction processing
### 2.1. Pre-execution (mempool & validation)
-  Tx is broadcast to the network and passed to CometBFT.
- `CheckTx`(via Baseapp) verifies the tx and admits it to the mempool.
### 2.2. Block inclusion & message handling
- After consensus, CometBFT includes the tx in a block and calls `DeliverTx`.
- `DeliverTx`
	- Runs the AnteHandler (nonce, gas, balance, etc.)
	- Routes the message to EVM's MsgServer: `x/vm/keeper/msg_server.go`
## 3. EVM execution (ApplyTransaction)
### 3.1. Setup
- Load EVM config (baseFee, coinbase, params)
- Convert the tx to `ethtypes.Message`.
- Create a `CacheContext` for rollback if the tx or hooks fail.
### 3.2. State Transition (ApplyMessageWithConfig)
- Create `StateDB` and initialize `EVM` instance.
- Calculate **intrinsic gas** using go-ethereum's protocol params which defines gas usage of each action.
- Deduct intrinsic gas from `leftoverGas` obtained from `msg.Gas()` which is gas received from user.
- Prepare the **access list**.
	- Normally done in AnteHandler.
	- But needed here for `eth_call`, `eth_estimateGas`.
### 3.3. EVM execution
- If contract creation: call `evm.Create`
- Else: call `evm.Call`
- Track remaining gas and refund amount (per EIP rules)
### 3.4. Gas & Refund accounting
- Cap gas refund using `RefundQuotient` (EIP-3529 aware)
- Calculate final `gasUsed`
- Refund leftover gas to sender (via Cosmos Bank module `SendCoinsFromModuleToAccount`)
	- *Cosmos doesn't refund gas to sender since it's more like a bid for priority inclusion and spam prevention.*
### 3.5. Post-execution
- Create `Receipt` with logs, bloom filter, contract address
- Run `PostTxProcessing` hooks (no gas charged here)
- Update bloom/log state tracking
- Commit `StateDB` if no errors
- Update transient state tracking
- Reset Cosmos gas meter(`ResetGasMeterAndConsumeGas`) to match EVM's `gasUsed`
	- Gas is not only added during the EVM execution but also in the SDK context(e.g., store operations, signature checks, etc.)
- `ResetGasMeterAndConsumeGas`
	- Reset the gas count
	- Consume evm execution `gasUsed`
## 4. Finalization
- Emit events.
- Return `MsgEthereumTxResponse`.
