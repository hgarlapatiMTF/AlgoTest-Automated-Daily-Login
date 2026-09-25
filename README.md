# AlgoTest Automated Daily Login

This repository automates your daily AlgoTest login every weekday morning, so your broker sessions are always fresh and ready for algo trading — without you having to do anything manually.

**How it works:** A secure server triggers this workflow each morning at your scheduled time. The workflow logs in to [algotest.in](https://algotest.in) using your credentials, which are stored as encrypted secrets in your own GitHub account and never visible to anyone else.

---

## Setup — 3 steps, takes about 5 minutes

### Step 1 — Fork this repository

Click the **Fork** button at the top-right of this page.

Make sure you fork it to **your personal GitHub account** (not an organisation).  
Keep the repository name as-is — **do not rename it**.

---

### Step 2 — Add your secrets

Your login credentials are stored as encrypted GitHub secrets. Only you and GitHub can see them — not the admin, not anyone else.

1. In your forked repository, go to **Settings** (top menu bar)
2. In the left sidebar click **Secrets and variables → Actions**
3. Click **New repository secret** and add each of the following:

| Secret name | What to put in it |
|---|---|
| `CLIENT_ID` | Your **10-digit mobile number** registered on AlgoTest (numbers only, no spaces, no +91) |
| `AT_PASSWORD` | Your **AlgoTest account password** |
| `LOGIN_SERVER_URL` | The URL the admin gives you (ask them for this — do not share it publicly) |

Add them one at a time. Each one is encrypted the moment you save it.

> ⚠️ **Never put your credentials in any file in this repo** — only ever in Secrets as above.

---

### Step 3 — Install the GitHub App

The admin's server needs permission to trigger your workflow each morning. You grant this by installing a GitHub App on your fork.

1. Click this link to install the app: **[Install AlgoTest Login App](#)** *(admin will replace this with the real link)*
2. On the installation page, select **Only select repositories**
3. Choose **your fork** of this repository from the dropdown
4. Click **Install**

That's it. The admin will confirm once you're scheduled and active.

---

## What happens every weekday morning

At your scheduled IST time (set by the admin):

1. The server triggers your workflow automatically
2. The workflow logs in to AlgoTest with your credentials
3. Any broker "Login" buttons visible in the AlgoTest dashboard sidebar are clicked automatically
4. You receive a Telegram message — ✅ if successful, ❌ with an error if something went wrong

No action is needed from you on any normal day.

---

## Manual trigger (optional)

If you want to run the login yourself at any time — for example, to test that everything is set up correctly:

1. Go to the **Actions** tab in your forked repository
2. Click **AlgoTest Daily Login** in the left sidebar
3. Click **Run workflow → Run workflow**

You'll receive a Telegram message with the result within a minute or two.

---

## Troubleshooting

**I got a ❌ failure message on Telegram**  
The workflow retries 3 times automatically before reporting a failure. If it still fails, the most common causes are:
- Your AlgoTest password recently changed → update the `AT_PASSWORD` secret
- AlgoTest showed an OTP / SMS verification screen (this can happen on new devices or IPs) → contact the admin

**I didn't get any Telegram message**  
- Check the **Actions** tab in your fork to see if the workflow ran at all
- Make sure the GitHub App is still installed (Settings → Integrations → GitHub Apps)
- Contact the admin — they can see your login result on their end too

**The workflow shows "Resource not accessible by integration"**  
The GitHub App was either not installed, or was installed on the wrong repository. Re-do Step 3 and make sure you selected your fork specifically.

**I need to change my password**  
Go to **Settings → Secrets and variables → Actions**, click the pencil icon next to `AT_PASSWORD`, and enter the new value. The next scheduled run will use it automatically.

---

## Security notes

- Your credentials are stored only in **your own GitHub account** as encrypted secrets
- The login server receives your credentials over HTTPS on each run and uses them only to drive the browser — they are never logged or stored anywhere on the server
- The GitHub OIDC token in the workflow proves to the server that the request came from your specific fork — it cannot be replayed or spoofed by anyone else
- You can revoke the GitHub App's access at any time: Settings → Integrations → GitHub Apps → Configure → Uninstall
