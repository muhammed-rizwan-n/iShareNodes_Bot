# Client Bot – iShareNodes

The **Client Bot** in iShareNodes is the main interface through which users interact with the platform. This bot handles all front-facing operations such as user registration, contribution commands, status checks, and notifications. It runs entirely on Discord, providing a seamless and familiar experience for participants.

---

## 🧑‍💻 1. User Registration

Account creation begins in the `#create_account` Discord channel, where users interact through **Discord modals** (popup input forms). This eliminates the need for traditional commands and improves usability, especially on mobile.

Once a user submits their email via the modal, the input is passed to the **Master Bot**, which handles:

- Generating a secure, time-based token (TOTP)
- Sending the token to the user's email
- Verifying the token when the user submits it back
- Creating and storing the user profile

This modular handoff between the **Client Bot** (UI/interaction) and the **Master Bot** (logic/database) ensures a clean separation of responsibilities while keeping the client side lightweight and responsive.

### Screenshots
<p float="left">
  <img src="./screenshots/register1.png" width="30%" />
  <img src="./screenshots/register2.png" width="30%" />
  <img src="./screenshots/dashboard.png" width="30%" />
</p>
### Screenshots
<p float="left">
  <img src="./screenshots/register1.png" width="30%" />
  <img src="./screenshots/register2.png" width="30%" />
  <img src="./screenshots/dashboard.png" width="30%" />
</p>


---
## 💼 2. Deposits, Withdrawals & Masternode Allocation

All operations in the client interface are fully interaction-based and presented as **ephemeral messages** — responses that are only visible to the requester. This makes it possible to use a **shared public channel** for all interactions without exposing private user data.

---

### 🔁 Deposits

Users initiate deposits using interactive buttons. Upon interaction:
- A **modal** appears for users to enter the amount.
- A **selection box** (dropdown) is presented to choose a cryptocurrency.
- Once submitted, the request is passed to the **Master Bot**, which verifies input and updates the user’s wallet.

> If the number of supported coins exceeds Discord’s selection box limit, a **pagination system** dynamically loads the next batch of coin options, ensuring a smooth and complete experience.
<p float="left">
  <img src="./screenshots/IMG-20250430-WA0017.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0022.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0019.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0014.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0012.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0011.jpg" width="30%" />

</p>
---

### 💸 Withdrawals

Withdrawals follow a similar interaction pattern:
- Users select the coin and amount via modals.
- Confirmations are shown via ephemeral messages.
- The **Master Bot** processes the withdrawal, deducts balance, and forwards the request to the **Wallet Bot** for actual transaction handling.

All confirmations and status updates are sent as ephemeral messages, maintaining user privacy.
<p float="left">
    <img src="./screenshots/IMG-20250430-WA0005.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0006.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0007.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0008.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0009.jpg" width="30%" />
  <img src="./screenshots/IMG-20250430-WA0010.jpg" width="30%" />

</p>
---

### 📊 Masternode Allocation View

Each user can view how their deposited funds have been allocated across different masternodes:
- Interactive dashboards show allocations per coin.
- A clean, scrollable layout is rendered using Discord embeds or paginated buttons.
- Data is presented as a **sorted, timestamped log** of contributions and their associated node shares.

> Behind the scenes, **data structures and algorithms (DSA)** like sorted lists and time-indexed maps are used to:
- Sort user deposits and rewards by date.
- Merge daily rewards into clear, digestible summaries.
- Aggregate rewards efficiently for display and analytics.

This design keeps interactions lightweight on the client while offloading processing to the Master Bot.

### Screenshots
<p float="left">
  <img src="./screenshots/deposit1.png" width="30%" />
  <img src="./screenshots/withdraw1.png" width="30%" />
  <img src="./screenshots/allocation1.png" width="30%" />
</p>

---

## 📈 3. Status & Rewards Tracking

The bot allows users to check the status of their investment, see masternode uptime, and track rewards received over time.

### Key Features:
- `/status` command shows current contribution and rewards.
- Notifications when rewards are distributed.
- Simple charts or summaries on request.

### Screenshots
<p float="left">
  <img src="./screenshots/status1.png" width="30%" />
  <img src="./screenshots/status2.png" width="30%" />
  <img src="./screenshots/rewards1.png" width="30%" />
</p>

---

## 💼 4. Deposits & Withdrawals

Users can deposit funds to their wallet address and request withdrawals through the bot. This ensures ease of liquidity and access to funds when needed.

### Key Features:
- `/deposit` command provides a unique wallet address.
- `/withdraw` initiates a withdrawal request with confirmation.
- View pending and completed transactions.

### Screenshots
<p float="left">
  <img src="./screenshots/deposit1.png" width="30%" />
  <img src="./screenshots/withdraw1.png" width="30%" />
  <img src="./screenshots/withdraw2.png" width="30%" />
</p>

---

> All screenshots were taken from a mobile device and resized using width attributes for clarity. Keep the original large images in the `./screenshots/` folder for full resolution reference.

---

### 📄 License
This document is for informational purposes only. Source code is private.


