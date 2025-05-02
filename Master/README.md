# 🧠 Master Bot – iShareNodes

The **Master Bot** is the core logic processor of the iShareNodes architecture. It handles all backend operations, manages secure communications between bots, and processes the majority of sensitive user operations.

---

## 🧩 Role of the Master Bot

The Master Bot is responsible for interpreting, validating, and responding to user-related operations passed through the Client and Wallet bots.

### ⚙️ User-Related Operations
- `create_account` – Register a new user
- `verify_account` – Validate email + token for login
- `create_deposit_address` – Generate wallet deposit address
- `change_withdrawal_address` – Change to current withdrawal address.
  
### 💼 Wallet and Finance Operations
- `add_deposits` – Confirm and record new deposits
- `add_rewards` – Append node-generated rewards
- `withdrawal_request` – Process and track user withdrawal
- `authenticate_user` – Cross-check identity and action intent
- `coin_data_updation` – Update coin/token metadata

### 🧩 Miscellaneous Operations
Besides core user and wallet functionality, the Master Bot handles various internal utilities that keep the system secure and maintainable.
- `decrypt_request` – Decrypts encrypted messages sent via Discord webhooks by the Client or Wallet bot. This ensures that only authenticated, intentional requests are processed.
- `notify_error` – Reports unexpected failures or logic errors by sending logs or alerts to the admin or logging channel.
- `validate_user_interaction` – Validates that the request originated from a legitimate Discord interaction. Helps in ensuring that requests are not spoofed or replayed after expiry.
- `log_activity`  – Records internal actions (such as deposits, verifications, etc.) in a secure, internal-only log to assist in audits and debugging.
- `send_email` – Sends authentication token or alerts to users via email for account verification and other secure interactions.
- `create_auth_key` – Generates a time-based one-time password (TOTP) secret for 2FA during account creation or recovery.
- `cancel_withdrawal` – Cancels a pending withdrawal when user request.
- `last10` – Checks deposit history and flags any amounts deposited within the last 10 days as locked, enforcing the platform’s deposit lock policy.

---

## 🔐 Secure Bot-to-Bot Communication via Discord Webhooks

Discord **does not allow direct communication** between bots. To overcome this, I have implemented a **webhook-driven communication model** that allows the Client and Wallet bots to securely transmit requests to the Master Bot.

### 🧪 How it Works:
1. When a user performs an interaction (via a button or modal), Discord generates:
   - `interaction_id`
   - `interaction_token`
2. The Client/Wallet bot encrypts the request payload using internal encryption methods.
3. This encrypted payload is passed to the Master Bot via a **Discord Webhook**.
<p><img src="./screenshots/IMG-2025-WA0001.png" />  <p/>
4. The Master Bot decrypts the request and **validates its authenticity** using the Discord interaction ID/token.
   
```sh
async def decrypt_request(x,content):
    try:
        content = json.loads(content)
        fernet = Fernet(decrypt_key(x['key']))
        content = fernet.decrypt(content.encode()).decode()
        x.update(content)
        return x,True
    except Exception as error:
        return x,False
```
5. If validation passes, the request is processed; if not, it's rejected silently.

```sh
async def validate_user_interaction(x):
    async with aiohttp.ClientSession() as session:
        async with session.get(f"https://discord.com/api/webhooks/{x['application_id']}/{x['interaction_token']}/messages/@original") as response:
            #print_logger(await response.json(),x['application_id'],x['interaction_token'])
            try:
                if await response.json()['interaction_metadata']['user']['id'] == x['discord_id'] and await response.json()['interaction_metadata']['timestamp'] == x['time']:
                    return True
                else:
                    return False
            except Exception as err:
                print_logger("User check failed:",err)
                return False
```

> ✅ This mechanism ensures:
- Bot-to-bot data flow without exposing endpoints
- Request origin is bound to a valid Discord interaction
- End-to-end verification of user intent
- Full security against replay or spoofed attacks

> 🔐 Every message is encrypted and **cannot be decrypted** without the Master Bot’s internal key and the originating interaction metadata — making it crack-proof.

---

## 🔁 Concurrency Handling & Transaction Safety

One of the crucial challenges in bot-based systems is ensuring the integrity of critical operations like withdrawals, especially when requests come in simultaneously from multiple devices or sessions using the same Discord account.

### Example:
A user attempts to initiate a withdrawal from **two devices** (or Discord clients) at nearly the same time. If both requests are processed without coordination, they may **bypass balance checks**, leading to **double withdrawal** or inconsistency in funds.

### iShareNodes' Approach:
To prevent this, the Master Bot maintains an **in-memory list of active user requests**, specifically tracking Discord user IDs currently undergoing sensitive operations.

#### Logic Flow:
1. When a withdrawal request is received, the Master Bot checks if the user’s ID is **already present in the active requests list**.
2. If yes, the new request is **immediately rejected**, ensuring that **only one request per user** is processed at any given time.
3. Once the operation completes (success or failure), the ID is removed from the list, allowing new requests.

This method prevents race conditions and overlapping logic execution, without requiring a database-level lock or external queueing system.

This approach ensures:
- **Zero conflict** between concurrent requests
- **Fast validation** using lightweight in-memory data
- **Clear rejection** of overlapping requests to the user

---

This bot acts as the **brain** of iShareNodes, ensuring trust, integrity, and efficiency at every step of the user experience.
