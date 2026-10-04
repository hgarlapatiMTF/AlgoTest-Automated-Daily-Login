# AlgoTest Automated Daily Login

This repository automates your daily AlgoTest login every weekday morning — including logging in your connected brokers (Kotak Neo, Upstox, Flattrade) — so your trading strategies are ready to run without you having to do anything manually.

You will receive a message every morning on Telegram via **@Algotest_daily_login_bot** confirming whether your login succeeded or failed.

---

## How your credentials are handled

This is the most important thing to understand before you set this up.

**Where they live.** Your passwords and TOTP secrets are stored as GitHub Secrets inside your own forked repository — not in a database, spreadsheet, or file the admin controls. Once you save a secret, GitHub never displays its value again to anyone, for any reason — not to another app, not to an API call, and not even to you or the admin looking at your own repo's Settings page. That is a permanent platform guarantee, not something either of you can turn off or bypass.

**When they are used.** Once a day, at your scheduled time, your own GitHub Actions workflow reads those secret values and sends them once — over an encrypted connection — to the login server, for the single purpose of typing them into your broker's login page on your behalf. The server does not write them to a database or log file. They exist in memory for the few seconds the login takes, then are gone.

**What the admin cannot do.** The admin cannot retrieve or view your stored secret values, ever — GitHub blocks that for everyone, permanently. The automation also cannot do anything beyond that one login step: it cannot place trades, transfer funds, change your broker settings, or take any action on your account other than establishing your daily session.

Two other pieces are involved:

- **Your GitHub OIDC token** proves to the server that a specific workflow run belongs to your account. It is short-lived (valid for a single run) and generated automatically by GitHub — you never see or handle it.
- **The admin's GitHub App**, which you install in Step 3 below, is only used to send your repository a "please run your workflow now" signal on schedule. It cannot read your GitHub Secrets under any circumstance — that is not a configuration choice, it is the same GitHub platform guarantee mentioned above.

---

## What you will receive on Telegram every morning

**On success:**
```
Login SUCCESS
Phone: 98XXXXXXXX
Brokers: Kotak Neo: LOGGED IN | upstox New: LOGGED IN
```

**On failure:**
```
Login FAILED
Phone: 98XXXXXXXX
Error: (reason for failure)
```

The workflow retries 3 times automatically before sending a failure message.

---

## Setup — 4 steps, about 5 minutes

### Step 1 — Fork this repository

Click the **Fork** button at the top-right of this page.

- Fork it to your **personal GitHub account** (not an organisation)
- **Do not rename the repository** — keep the name exactly as-is

---

### Step 2 — Add your secrets

Go to your forked repository → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

Add the secrets that apply to you. Only add what is relevant to your brokers — skip the rest.

#### Required for everyone

| Secret name | What to enter |
|---|---|
| `PHONE_NUMBER` | Your **10-digit mobile number** registered on AlgoTest (no spaces, no +91) |
| `AT_PASSWORD` | Your **AlgoTest account password** |

#### Kotak Neo users

| Secret name | What to enter |
|---|---|
| `KN_CLIENT_ID` | Your Kotak Neo **Client ID** (e.g. `AB1234`) |
| `KN_MOBILE` | Your **registered mobile number** on Kotak (10 digits, no +91) |
| `KN_TOTP_SECRET` | Your Kotak TOTP secret key (alphanumeric code from authenticator setup) |
| `KN_PIN` | Your Kotak **6-digit MPIN** |

#### Upstox users

| Secret name | What to enter |
|---|---|
| `TOTP_SECRET` | Your Upstox TOTP secret key (alphanumeric code from authenticator setup) |
| `PIN` | Your Upstox **6-digit login PIN** |

#### Flattrade users

| Secret name | What to enter |
|---|---|
| `U` | Your Flattrade **User ID** (e.g. `FZ12345`) |
| `P` | Your Flattrade **account password** |
| `T` | Your Flattrade TOTP secret key (alphanumeric code from authenticator setup) |

> **Where do I find my TOTP secret key?**
> It is the alphanumeric code (looks like `JBSWY3DPEHPK3PXP`) shown when you first set up the authenticator app for your broker. If you no longer have it, reset your 2FA on the broker's website — the new code shown during that setup is your TOTP secret key.

---

### Step 3 — Install the GitHub App

The admin's server needs permission to trigger your workflow each morning.

1. Click this link: **[Install AlgoTest Login App](https://github.com/apps/algotest-login-scheduler)**
2. On the installation page, select **Only select repositories**
3. Choose **your fork** of this repository
4. Click **Install**

---

### Step 4 — Message the Telegram bot

Open Telegram and send a message to **@Algotest_daily_login_bot**:

```
Hi, setup complete. My GitHub username is: your-github-username
```

This is the only thing you need to do to notify the admin. Once your message is received, you will be added to the daily schedule and will start receiving login confirmations from the next working day. No other contact is needed.

---

## What happens every weekday morning

At your scheduled time (set by the admin, typically 8:00–8:15 AM IST):

1. The server triggers your workflow automatically
2. Logs in to **AlgoTest** with your phone number and password
3. Logs in your connected brokers one by one (Kotak Neo, Upstox, Flattrade — whichever you have set up)
4. Sends you a **success or failure message** on Telegram via **@Algotest_daily_login_bot**

You do not need to do anything on normal days.

---

## Secrets quick reference

| Secret | Required for | Example |
|---|---|---|
| `PHONE_NUMBER` | Everyone | `9876543210` |
| `AT_PASSWORD` | Everyone | `MyAlgoPass@123` |
| `KN_CLIENT_ID` | Kotak Neo | `AB1234` |
| `KN_MOBILE` | Kotak Neo | `9876543210` |
| `KN_TOTP_SECRET` | Kotak Neo | `JBSWY3DPEHPK3PXP` |
| `KN_PIN` | Kotak Neo | `123456` |
| `TOTP_SECRET` | Upstox | `JBSWY3DPEHPK3PXP` |
| `PIN` | Upstox | `123456` |
| `U` | Flattrade | `FZ12345` |
| `P` | Flattrade | `MyFlatPass@123` |
| `T` | Flattrade | `ABCDEFGHIJKLMNOP` |

---

## Troubleshooting

**I received a failure message**

Common causes:

- **Wrong password** — update the relevant secret and the next run will use the new value
- **Wrong TOTP secret** — the key does not match what your broker has on file; reset 2FA on the broker's website, save the new key as your secret
- **Wrong PIN or MPIN** — update the relevant secret
- **AlgoTest showed an extra verification screen** — message **@Algotest_daily_login_bot** and the admin will check what happened

**I did not receive any Telegram message**

- Check the **Actions** tab in your fork — did the workflow actually run?
- Make sure the GitHub App is still installed: **Settings → Integrations → GitHub Apps**
- Message **@Algotest_daily_login_bot** and the admin will follow up

**I need to update a password, PIN or TOTP secret**

Go to **Settings → Secrets and variables → Actions**, click the pencil icon next to the secret, enter the new value and save. The change takes effect on the very next login run.

**I want to add a broker I did not set up initially**

Add the relevant secrets for that broker (see the table above) and message **@Algotest_daily_login_bot** so the admin can confirm the broker is connected on your AlgoTest account.

**I want to stop the automation**

Go to **Settings → Integrations → GitHub Apps → Configure → Uninstall**. This removes the server's permission to trigger your workflow. Your secrets remain safely in your account and are not deleted.

---

## Security

- Your credentials are stored only in **your own GitHub account** as encrypted secrets — the admin cannot see them
- Credentials are transmitted to the login server over HTTPS only during the login run and are never stored anywhere
- The GitHub OIDC token is unique to your fork and valid only for a single workflow run — it cannot be reused or faked
- Login result messages are sent only to your own Telegram chat
