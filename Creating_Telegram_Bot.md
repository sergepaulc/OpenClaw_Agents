### Creating a Telegram Bot for OpenClaw

#### 1) Create a Telegram account (if needed)

Install Telegram on your phone and sign up with your phone number.

#### 2) Create a new bot with BotFather

1. Open Telegram and search: **@BotFather**  
2. Start the chat and send:  

/newbot

#### 3) When prompted, choose:

* **Bot name** (display name): e.g., `OpenClaw Assistant`
* **Bot username** (must be unique and end in `bot`): e.g., `openclaw_sophie_bot`

4. BotFather will return an **HTTP API token** that looks like:
   `123456789:AA...`

✅ Copy this token (treat it like a password).

#### 4) Connect the bot token to OpenClaw

In WSL/Ubuntu run:

openclaw onboard

Then choose:

* **Channel** → Telegram (Bot API)
* Paste your **bot token** when prompted

#### 4) Start the bot chat

In Telegram:

1. Search for your bot username (e.g. `@openclaw_sophie_bot`)
2. Open it and tap **Start**
3. Send a message like:

hello

#### 5) If OpenClaw says “access not configured”

This means OpenClaw is in allowlist/pairing mode.

* Message **@userinfobot** to get your Telegram **numeric user ID**
* Add that ID to OpenClaw’s Telegram allowlist (`allowFrom`) and restart OpenClaw:

openclaw daemon restart

---

## Security notes

* Do **not** share your bot token publicly.
* If you accidentally expose it, rotate it immediately:

  * @BotFather → `/mybots` → select bot → **API Token** → **Revoke** / regenerate
  * Update the token in OpenClaw and restart
