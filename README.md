# AlgoTest Automated Daily Login

This repository automates your daily AlgoTest login every weekday morning — including logging in your connected brokers (KotakNeo,Upstox, Flattrade) — so your trading strategies are ready to run without you having to do anything manually.

You will receive a message on Telegram via **@Algotest_daily_login_bot** every morning confirming whether your login succeeded or failed.

---

## What you will receive on Telegram every morning

**On success:**
```
Login SUCCESS
Phone: 98XXXXXXXX
Brokers: upstox New: LOGGED IN | Flattrade: LOGGED IN
```

**On failure:**
```
Login FAILED
Phone: 98XXXXXXXX
Error: (reason for failure)
```

If it fails, the workflow automatically retries 3 times before sending the failure message.

---

## Setup — 4 steps, about 5 minutes

### Step 1 — Fork this repository

Click the **Fork** button at the top-right of this page.

- Fork it to your **personal GitHub account** (not an organisation)
- **Do not rename the repository** — keep the name exactly as-is

---

### Step 2 — Add your secrets

Go to your forked repository → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add the secrets that apply to you based on which brokers you have connected on AlgoTest:

#### Everyone must add these 2 secrets:

| Secret name | What to enter |
|---|---|
| `PHONE_NUMBER` | Your **10-digit mobile number** registered on AlgoTest (no spaces, no +91) |
| `AT_PASSWORD` | Your **AlgoTest account password** |

#### If you have Upstox connected on AlgoTest, also add:

| Secret name | What to enter |
|---|---|
| `TOTP_SECRET` | Your Upstox TOTP secret key (the alphanumeric code shown when you set up your Upstox authenticator app) |
| `PIN` | Your Upstox **6-digit login PIN** |

#### If you have Flattrade connected on AlgoTest, also add:

| Secret name | What to enter |
|---|---|
| `U` | Your Flattrade **User ID** (e.g. FZ12345) |
| `P` | Your Flattrade **account password** |
| `T` | Your Flattrade TOTP secret key (the alphanumeric code shown when you set up your Flattrade authenticator app) |

#### If you have Kotak Neo connected on AlgoTest, also add:

| Secret name | What to enter |
|---|---|
| `KN_CLIENT_ID` | Your Kotak Neo **Client ID** (e.g. AB1234) |
| `KN_MOBILE` | Your **registered mobile number** on Kotak |
| `KN_TOTP_SECRET` | Your Kotak TOTP secret key (from authenticator setup) |
| `KN_PIN` | Your Kotak **6-digit MPIN** |

> **Add only the secrets that apply to your brokers.** Mix and match freely. If you only have Upstox, skip U, P, T. If you only have Flattrade, skip TOTP_SECRET and PIN.

> **Where do I find my TOTP secret key?**
> It is the alphanumeric code (looks like `JBSWY3DPEHPK3PXP`) shown when you first set up the authenticator app for your broker. If you no longer have it, reset your 2FA on the broker's website — the new code shown during that setup is your TOTP secret key.

---

### Step 3 — Install the GitHub App

The admin's server needs permission to trigger your workflow each morning.

1. Click this link to install: **https://github.com/apps/algotest-login-scheduler**
2. On the installation page, select **Only select repositories**
3. Choose **your fork** of this repository
4. Click **Install**

---

### Step 4 — Message the Telegram bot

Open Telegram and send any message to **@Algotest_daily_login_bot** — for example:

```
Hi, I have completed the setup. My GitHub username is: your-github-username
```

This is how the admin knows you are ready. Once your message is received, you will be added to the schedule and will start receiving daily login confirmations from the next working day.

You do not need to contact the admin through any other channel — the bot message is all that is needed.

---

## What happens every weekday morning

At your scheduled time (set by the admin, typically 8:00–8:15 AM IST):

1. The server triggers your workflow automatically
2. Logs in to **AlgoTest** with your phone number and password
3. Logs in your connected brokers (KotakNeo\Upstox\Flattrade)
4. Sends you a **success or failure message** on Telegram via **@Algotest_daily_login_bot**

You do not need to do anything on normal days.

---

## Secrets quick reference

| Secret | Required for | Example |
|---|---|---|
| `PHONE_NUMBER` | Everyone | `9876543210` |
| `AT_PASSWORD` | Everyone | `MyAlgoPass@123` |
| `TOTP_SECRET` | Upstox users | `JBSWY3DPEHPK3PXP` |
| `PIN` | Upstox users | `123456` |
| `U` | Flattrade users | `FZ12345` |
| `P` | Flattrade users | `MyFlatPass@123` |
| `T` | Flattrade users | `ABCDEFGHIJKLMNOP` |
| `KN_CLIENT_ID` | Kotak Neo users | `AB1234` |
| `KN_MOBILE` | Kotak Neo users | `9876543210` |
| `KN_TOTP_SECRET` | Kotak Neo users | `JBSWY3DPEHPK3PXP` |
| `KN_PIN` | Kotak Neo users | `123456` |

---

## Troubleshooting

**I received a failure message**

The workflow retries 3 times before reporting failure. Common causes:

- **Wrong password** — update `AT_PASSWORD` in your secrets and try again
- **Wrong TOTP secret** — the key does not match what your broker has on file; reset 2FA on the broker's website and update the secret
- **Wrong PIN** — update `PIN` in your secrets
- **AlgoTest showed an extra verification step** — message **@Algotest_daily_login_bot** and the admin will check what happened

**I did not receive any Telegram message**

- Check the **Actions** tab in your fork — did the workflow run?
- Make sure the GitHub App is still installed: Settings → Integrations → GitHub Apps
- Message **@Algotest_daily_login_bot** and the admin will follow up

**I need to update my password, PIN or TOTP secret**

Go to **Settings → Secrets and variables → Actions**, click the pencil icon next to the secret, enter the new value and save. The change takes effect on the next login.

**I want to add a broker I did not set up initially**

Add the relevant secrets (`TOTP_SECRET` + `PIN` for Upstox, or `U` + `P` + `T` for Flattrade) and message **@Algotest_daily_login_bot** so the admin can verify the broker is connected on your AlgoTest account.

**I want to stop the automation**

Go to **Settings → Integrations → GitHub Apps → Configure → Uninstall**. This removes the server's permission to trigger your workflow. Your secrets remain safely in your account.

---

## Security

- Your credentials are stored only in **your own GitHub account** as encrypted secrets — the admin cannot see them
- Credentials are sent to the login server over HTTPS only during the login run and are never stored
- The GitHub OIDC token used for authentication is unique to your fork and cannot be faked by anyone else
- Login result messages are sent only to your own Telegram chat
