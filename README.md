# BitVault: Decentralized Bitcoin-Backed Lending Protocol

**BitVault** is a decentralized, over-collateralized lending protocol that enables Bitcoin holders to unlock liquidity without selling their BTC. Built on the **Stacks** Layer 2, BitVault brings smart contract functionality to Bitcoin, allowing users to leverage their assets while retaining long-term exposure.

---

## 🌐 System Overview

BitVault allows users to:

* **Deposit BTC** as collateral via Stacks-Bitcoin bridges
* **Mint loans** against this collateral in a decentralized, non-custodial manner
* **Repay** loans with interest to unlock their collateral
* **Avoid liquidation** through health monitoring and real-time price feeds
* **Govern** key risk parameters via an on-chain governance mechanism

### Key Properties

| Feature                       | Description                                  |
| ----------------------------- | -------------------------------------------- |
| **Over-Collateralized Loans** | Min. 150% collateral ratio enforced          |
| **Liquidation Mechanism**     | Triggered at ≤ 120% collateral ratio         |
| **Multi-Asset Collateral**    | Support for BTC & STX (extensible)           |
| **Interest Rates**            | Configurable; default 5% annual              |
| **Price Feeds**               | Governance-controlled external oracle inputs |
| **Governance**                | Contract owner updates parameters & feeds    |

---

## ⚙️ Contract Architecture

The BitVault smart contract is implemented in **Clarity**, optimized for readability, security, and modularity. It consists of:

### 🔐 Protocol Constants

* `minimum-collateral-ratio`: Initial 150%
* `liquidation-threshold`: 120%
* `platform-fee-rate`: 1%

These are modifiable only by the contract owner via governance functions.

### 📦 Core Data Maps

* **`loans`**: Tracks all loan positions (by `loan-id`)
* **`user-loans`**: Maps users to active loans
* **`collateral-prices`**: Oracle price feed storage
* **`total-btc-locked`**: Platform-wide BTC metric

### 🧠 Key Functions

#### Public Functions

| Function                       | Description                                          |
| ------------------------------ | ---------------------------------------------------- |
| `initialize-platform`          | One-time contract activation                         |
| `deposit-collateral`           | Register BTC collateral (off-chain transfer assumed) |
| `request-loan`                 | Create a new loan after verifying collateral ratio   |
| `repay-loan`                   | Repay principal + interest to recover collateral     |
| `update-price-feed`            | Update BTC/STX prices (governance-controlled)        |
| `update-collateral-ratio`      | Adjust collateral requirement                        |
| `update-liquidation-threshold` | Adjust liquidation trigger                           |

#### Read-only Functions

| Function             | Returns                       |
| -------------------- | ----------------------------- |
| `get-loan-details`   | Loan struct for a given ID    |
| `get-user-loans`     | Active loan IDs for a user    |
| `get-platform-stats` | Collateral, loans, ratios     |
| `get-valid-assets`   | Whitelisted assets (BTC, STX) |

#### Private Functions

| Function                     | Purpose                                               |
| ---------------------------- | ----------------------------------------------------- |
| `calculate-collateral-ratio` | Checks if position meets requirements                 |
| `calculate-interest`         | Accrues interest per block                            |
| `check-liquidation`          | Triggers `liquidate-position` if under-collateralized |
| `validate-loan-id`           | Sanity checks                                         |
| `is-valid-asset`             | Validates whitelisted collateral                      |
| `liquidate-position`         | Marks loans as liquidated, updates maps               |

---

## 🔄 Data Flow

```
   [1] Initialize Contract
           |
           v
   [2] Deposit Collateral (off-chain BTC transfer assumed)
           |
           v
   [3] Request Loan → Verify Collateral Ratio via BTC price feed
           |
           v
   [4] Loan Approved → Stored in `loans` and `user-loans`
           |
           v
   [5] Interest Accrues Per Block → Calculated on Repayment
           |
           v
   [6a] Repay Loan → Collateral Unlocked
   [6b] Under-Collateralized → Auto Liquidation Triggered
```

---

## 🧱 System Assumptions

* BTC collateral is tracked off-chain and mirrored on-chain via wrapped BTC or trust-minimized bridges.
* Price feed updates are pushed via governance (eventually upgradable to decentralized oracle support).
* Borrower risk is managed through real-time LTV checks and liquidation triggers.

---

## 🛡️ Security Considerations

* **Access Control**: Only contract owner can update critical parameters and price feeds.
* **Interest Accrual**: Based on block height differences; capped to avoid overflows.
* **Price Feed Limits**: Sanity checks (e.g., upper bound: `1,000,000,000,000`) on inputs.
* **Loan Cap per User**: Max 10 active loans to prevent map overflows.

---

## 📈 Governance Model

The current implementation supports centralized governance (`CONTRACT-OWNER`) with plans to transition to DAO-controlled multisig or timelocked upgrades in future iterations.

---

## 🚀 Future Enhancements

* Integration with **decentralized oracles** (e.g., Chainlink, ZEST)
* Support for **liquid staking assets** as collateral
* **Loan tokenization** (NFT/FT positions)
* **Variable interest rates** via risk models
* DAO-based **governance** with STX voting

---

## 🧪 Testing & Deployment

1. **Deploy** to localnet/testnet using Clarinet or Hiro CLI
2. **Initialize platform** via `initialize-platform`
3. **Set price feeds** using `update-price-feed`
4. **Simulate lending flow**:

   * Deposit BTC
   * Request loan
   * Monitor liquidation triggers
   * Repay and retrieve collateral

> Note: Ensure off-chain BTC movements (e.g., via Wrapped BTC on Stacks) are aligned with on-chain states.

---

## 📂 File Structure

```
/contracts
 └── bitvault.clar      ; Main contract
/tests
 └── bitvault_test.ts   ; Test cases (Clarinet/TS)
README.md               ; Project documentation
```

---

## 📜 License

MIT License © 2025 BitVault Contributors

---

## 🤝 Contributing

Pull requests, audits, and feature proposals are welcome. For major changes, please open an issue to discuss.
