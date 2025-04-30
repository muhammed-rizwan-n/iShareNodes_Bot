# 💼 Wallet Bot – iShareNodes

The **Wallet Bot** is responsible for all operations involving user wallets and masternode funds. It works behind the scenes to ensure secure and accurate handling of deposits, withdrawals, reward distributions, and masternode allocations. This bot plays a critical role in fund management and financial integrity of the iShareNodes platform.

---

## 🧾 Responsibilities

### 1. 🔍 Track Deposits
- Monitors the blockchain for incoming user transactions.
- Updates user balance once the transaction is confirmed.
- Ensures every deposit is linked to the correct user and coin.

### 2. 🎁 Allocate Rewards
- Calculates user reward shares based on masternode performance.
- Compounds rewards into masternode pools where applicable.
- Ensures equal opportunity by distributing rewards fairly to all contributors.

### 3. 🏗️ Masternode Management
- Creates new masternodes when pooled collateral reaches threshold.
- Dynamically allocates user deposits across multiple masternodes.
- Maintains coin-wise distribution metrics to optimize load and uptime.

### 4. 💸 Withdrawals
- Processes valid withdrawal requests passed from the Master Bot.
- Ensures the amount is unlocked (not part of locked funds or rewards).
- Confirms the wallet address and deducts the requested amount securely.

### 5. ⚖️ Compounding & Allocation
- Handles reinvestment (compounding) of eligible user rewards.
- Matches contributions to active masternodes using internal strategies.
- Optimizes distribution to avoid bottlenecks or concentration of funds.

---

## 🔐 Security & Coordination

- All sensitive wallet functions are gated behind Master Bot authentication.

> The Wallet Bot never directly responds to users. It performs backend operations and sends success/error updates back via Master Bot, ensuring modularity and system-wide security.

---

## 🔒 Keypool Verification & Wallet Backup

To prevent wallet corruption and unauthorized key changes, the Wallet Bot implements a robust verification system using the `keypoololdest` value:

### 🧠 `keypoololdestcheck`
- Each time the wallet is loaded or updated, the `keypoololdest` value is fetched.
- This value is compared with the version stored in the database.
- If a mismatch is detected:
  - The wallet is **immediately paused** from going online.
  - An **error report** is sent to the admin for manual inspection.
  - All automated actions for the affected wallet are suspended to avoid fund mismanagement.

> This validation ensures that a newly created or corrupted wallet (which would have a different set of keys) is never mistakenly treated as the original one.

### 💾 `keypoolrefill` and Wallet Backup
- When refilling the key pool:
  - New addresses are generated and appended to the pool.
  - A fresh `wallet.dat` backup is created and securely stored.
  - The updated `keypoololdest` is saved in the database for future integrity checks.
- This guarantees that even in cases of software or storage failure, a **recoverable wallet state** is always available.

> These features work together to ensure the **authenticity, consistency, and recoverability** of the wallet at all times.

---

## 🐳 Dockerized Wallet Management

To ensure isolated, consistent, and scalable wallet environments, all cryptocurrency wallets managed by the Wallet Bot are run inside **Docker containers**.

### ⚙️ Architecture & Communication
- Each wallet runs in its own container, based on official or custom Docker images.
- The Wallet Bot interacts with the containers using the **Docker Python SDK**, enabling:
  - Programmatic access to wallet logs, status, and CLI commands
  - Controlled startup, shutdown, and restarts of wallet containers
  - Real-time monitoring of container health and uptime

### 🔒 Benefits
- **Isolation**: Each wallet operates in its own secure environment, minimizing cross-wallet risk.
- **Consistency**: Environments remain identical across deployments and updates.
- **Security**: Wallet files (`wallet.dat`, configs, logs) are mounted via Docker volumes and secured at the file system level.
- **Scalability**: New wallet containers can be deployed dynamically when a new coin or node type is added.

> By containerizing wallet daemons, iShareNodes ensures a robust and flexible infrastructure that’s easy to maintain and extend across multiple assets and nodes.

---

## 📁 Additional Capabilities

- Performs regular integrity checks on wallet balances and masternode states.
- Handles emergency wallet recovery, refund, or reallocation procedures.
- Tracks the lifecycle of each contribution from deposit to reward distribution.

---

### 📌 Example Functions (Internals)

- `get_deposits` – Watches for incoming user transactions and updates internal DB.
- `distribute_rewards` – Calculates and allocates daily rewards to contributors.
- `create_masternode` – Launches a new masternode instance when collateral is pooled.
- `compound_rewards` – Automatically reuses earned rewards as contributions.
- `handle_withdrawal` – Safely processes outgoing funds to verified wallet addresses.
- `refill_nodes` – Adjusts fund distribution across nodes.
- `missing_deposits` – Check for the missing deposits of the users, if found valid, the amount is credited to the account.
- `keypoolrefill` – Replenishes the wallet key pool by generating fresh addresses, creates a backup of the new `wallet.dat`, and updates the latest `keypoololdest` value to the database.
