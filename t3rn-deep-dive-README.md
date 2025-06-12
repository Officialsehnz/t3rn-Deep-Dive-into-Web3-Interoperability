
# 🔗 t3rn – Deep Dive into Web3 Interoperability

> A detailed breakdown of the t3rn project: its architecture, technology stack, consensus model, testnet history, and how it compares with similar protocols like LayerZero and Axelar.

---

## 📚 Table of Contents

1. [Overview](#overview)  
2. [Technology Stack](#technology-stack)  
3. [Consensus Mechanism](#consensus-mechanism)  
4. [The Problem t3rn Solves](#the-problem-t3rn-solves)  
5. [Architecture & Workflow](#architecture--workflow)  
6. [Testnets: V1 and V2](#testnets-v1-and-v2)  
7. [Comparison with Other Interop Protocols](#comparison-with-other-interop-protocols)  
8. [Conclusion](#conclusion)  

---

## 🧠 Overview

`t3rn` is a cross-chain execution and interoperability protocol built as a **Substrate-based parachain** on **Polkadot**. It enables **atomic multi-chain smart contract execution**, ensuring **all-or-nothing outcomes** when interacting across blockchains.

---

## 🛠 Technology Stack

- **Framework**: Built on **Substrate** (Rust)  
- **Runtime**: Custom pallets including:
  - `circuit` – workflow orchestrator  
  - `executor` – off-chain worker coordination  
  - `gateway` – blockchain-specific adapters  
  - `attesters` – proof validators (future)  

- **Smart Contract Support**:  
  - Accepts **ink!**, **Solidity**, and any **WASM-compatible** contract  
  - Contracts compiled & uploaded to a **shared on-chain registry**  
  - Includes a royalty model for contract authors

- **Gateways**:  
  - Integrates with EVM, WASM, IBC, and other blockchain types  
  - Operate via pluggable modules: `Intrinsic`, `Extrinsic`, or `Tx-only`  

- **Executors**:  
  - Off-chain agents written in **Rust/JS**  
  - Monitor, bid, and execute cross-chain workflows  
  - Connect via RPCs (e.g., Arbitrum, Optimism, Base)  
  - Submit batch proofs through config contracts like `SingleAttestationBook`  

- **Developer Tooling**:
  - CLI executor scripts  
  - JS SDKs  
  - Web UI for composing and submitting orders  

---

## 🔐 Consensus Mechanism

- **Base Layer**: Polkadot’s NPoS system  
  - **BABE** for block production  
  - **GRANDPA** for finality  

- **Execution Layer**:
  - Cross-chain workflows settled by t3rn’s **Circuit** pallet  
  - Validators ensure that either:
    - All steps succeed (`settled`), or  
    - All steps fail (`rollback`)  

- **Reverse Auction Bidding**:
  - Executors bid down from a user-defined max fee  
  - Winning executor escrows value, executes the order  
  - Only paid upon valid proof of successful execution  

---

## 🧩 The Problem t3rn Solves

> **Cross-chain complexity**: Existing bridges and messaging layers (LayerZero, Axelar, IBC) do not guarantee that multiple calls across chains will succeed or fail atomically.

**t3rn’s innovation**:
- A **Circuit-based protocol** that coordinates all steps of a transaction
- Ensures either **full success or safe reversion**
- **Avoids long-term liquidity lockups** and wrapping tokens

Example Use Case:  
```
1. Swap on Ethereum  
2. Move funds to Polkadot  
3. Stake on Moonbeam  
4. Collect yield in USDC  
5. Reinvest on another chain  
```

If any step fails, the entire flow is rolled back.

---

## 🧬 Architecture & Workflow

### Circuit Flow

1. **User submits intent** with max fee and step list  
2. **Circuit validates** logic and locks funds  
3. **Executors bid** to execute the workflow  
4. **Winner escrows funds**, executes on source & target chains  
5. **Gateways emit proofs**, executor submits them  
6. **Circuit checks proofs**  
   - If all OK → settle & pay executor  
   - If any fail → rollback and refund  

### Gateways

Each connected chain uses a specialized Gateway:
```
- Ethereum: contract-based gateway (escrow + proof emitters)
- Polkadot: runtime modules
- Cosmos: IBC channel handler
```

### No Liquidity Pools

t3rn avoids common bridge attack surfaces:
- Executors front capital temporarily  
- No persistent wrapped assets or global treasuries  

---

## 🧪 Testnets: V1 and V2

### 🔹 Testnet V1 (2024)

- Duration: Aug 2024 – Feb 2025  
- Processed:
  - 💸 $38.4B in volume  
  - 👥 114,729 users  
  - ⚙️ 9,079 executors  
- Issues:
  - High load, refund delays, BRN UX friction  
- Response:
  - Minimum swap thresholds, “Claim All Refunds”, UX fixes

---

### 🔸 Testnet V2 (2025)

- Launched: Mar 18, 2025  
- Features:
  - New **dynamic rewards model** (scaled to value and demand)  
  - Upgraded bridge UI  
  - Executor dashboard  
  - Expanded chain support (Base, Optimism, Arbitrum, etc.)  

- Monthly BRN Pool: `10M` BRN  
- Reward boosts: up to `+50%` for large flows  

---

## 🔁 Comparison with Other Interop Protocols

| Feature                  | t3rn                            | LayerZero                     | Axelar                        | XCM/XBI                     |
|--------------------------|----------------------------------|-------------------------------|-------------------------------|-----------------------------|
| Architecture             | Substrate parachain + executors  | Messaging + Oracle/Relayer    | PoS Gateway Chain             | Message format for parachains |
| Atomic Execution         | ✅ Yes (multi-chain rollback)     | ❌ No                          | ❌ No                          | ❌ No                        |
| Wrapped Tokens           | ❌ No                            | ✅ Yes                         | ✅ Yes                         | ❌ N/A                       |
| Capital Use              | Executor escrow per order        | Protocol-owned liquidity      | Validator-managed pools       | Relay message only          |
| Contract Registry        | ✅ Built-in, incentivized         | ❌                             | ❌                             | ❌                           |
| Cross-VM Support         | ✅ ink!, Solidity, WASM           | Limited to chain endpoints    | Limited to connected EVMs     | Only WASM ↔ WASM            |
| Chain Compatibility      | EVM, WASM, IBC, more             | Primarily EVM                 | EVM-focused                   | Only Polkadot parachains    |
| Security Model           | Polkadot NPoS + escrow proofs    | Oracle + Relayer trust        | PoS + validators              | Relay Chain validators      |

---

## 🚀 Conclusion

`t3rn` is **not just a bridge** — it's a trustless **cross-chain execution layer** that:
- Guarantees **atomicity** across multiple chains
- Avoids permanent liquidity lockups  
- Incentivizes **executor competition** for fair pricing  
- Hosts a decentralized **contract library**  
- Leverages Polkadot’s secure consensus model  

### Coming Soon:
- Ethereum light clients  
- TRN token launch  
- Mainnet deployment  

> Ideal for developers building **multi-chain apps**, wallets, or protocols requiring **secure, rollback-capable workflows**.

---

## 📎 Resources

- [t3rn Protocol Docs](https://docs.t3rn.io)
- [t3rn GitHub](https://github.com/t3rn)
- [t3rn Twitter/X](https://twitter.com/t3rn_io)
- [t3rn Blog](https://blog.t3rn.io)
- [Parachain Slot](https://parachains.info/details/t3rn)
