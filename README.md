# OpenClaw Agents



## Editing Powerpoint presentations

````markdown
# OpenClaw PPTX Editor (Windows + WSL2 + Telegram)

Edit PowerPoint `.pptx` files **from a Telegram chat** using **OpenClaw** on a Windows PC (via WSL2 + Ubuntu).  
This project edits the **PPTX file directly** (Open XML) — **no PowerPoint UI automation**.

## What you can do

Send a Telegram message like:

```text
Edit PPTX Art_Class.pptx: replace "Class" with "Course" on all slides
````

or:

```text
Edit PPTX Art_Class.pptx: replace "Class" with "Course" on slide 3
```

OpenClaw will:

* find the file in a dedicated folder on your Desktop
* apply the text change (all slides or one slide)
* save a new file: `*_edited_<timestamp>.pptx` (never overwrites the original)
* reply in Telegram with the output filename and `changes=N`

---

## Architecture (high level)

* **Telegram Bot** (UI): you chat with your bot on your phone
* **OpenClaw Gateway** (WSL Ubuntu): receives Telegram messages and routes to the agent
* **Safe Exec Wrapper** (`pptx_edit_safe.sh`): enforces folder boundaries + allowed command
* **Python PPTX Editor** (`pptx_edit.py`): edits PPTX using `python-pptx` and writes a new file

---

## Requirements

### Windows

* Windows 10/11 with **WSL2 enabled**
* Ubuntu 24.04 installed in WSL

### OpenClaw

* OpenClaw installed on WSL Ubuntu
* A configured model provider key (Anthropic/OpenAI/etc.)
* Telegram bot token configured in OpenClaw
* Telegram access configured (allowlist/pairing as desired)

### Python (WSL)

* Python 3
* `python3-venv` (recommended)
* `python-pptx` installed inside a venv

---

## Folder boundary (Windows)

Create a dedicated folder on your Windows Desktop:

* `C:\Users\<YOU>\Desktop\OpenClaw_PPT\`

WSL path:

* `/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT`

Only PPTX files in this folder are eligible for editing.

---

## Setup

### 1) Create the Desktop folder (Windows)

Create:

* `Desktop\OpenClaw_PPT`

Put your input decks here (e.g., `Art_Class.pptx`).

### 2) Create a Python virtual environment (WSL Ubuntu)

```bash
sudo apt update
sudo apt install -y python3-venv

python3 -m venv ~/.venvs/openclaw-ppt
source ~/.venvs/openclaw-ppt/bin/activate
pip install --upgrade pip
pip install python-pptx
python -c "import pptx; print('pptx ok')"
```

### 3) Add the PPTX edit script (WSL)

Create:

* `~/tools/pptx_edit.py`

```bash
mkdir -p ~/tools
nano ~/tools/pptx_edit.py
chmod +x ~/tools/pptx_edit.py
python ~/tools/pptx_edit.py --help
```

Example script responsibilities:

* open `.pptx`
* find/replace in text runs
* optionally limit to a slide number
* write `*_edited_<timestamp>.pptx`

> Ensure your script prints: output path and `changes=N`.

### 4) Add the safe wrapper script (WSL)

Create:

* `~/tools/pptx_edit_safe.sh`

This wrapper must:

* accept only a **filename** (no paths)
* force input to `/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT/<filename>`
* ensure `.pptx` extension
* call the venv python + `pptx_edit.py`
* write output to the same folder

```bash
nano ~/tools/pptx_edit_safe.sh
chmod +x ~/tools/pptx_edit_safe.sh
```

Manual test:

```bash
PPT_DIR="$(ls -d /mnt/c/Users/*/Desktop/OpenClaw_PPT | head -n 1)"
~/tools/pptx_edit_safe.sh Art_Class.pptx Class Course
```

You should see a new file like:

* `Art_Class_edited_YYYYMMDD-HHMMSS.pptx`

### 5) Configure OpenClaw to run only the wrapper (Exec approvals/allowlist)

For security, OpenClaw should be allowed to execute **only**:

* `/home/<LINUX_USER>/tools/pptx_edit_safe.sh`

Recommended approach:

* keep Exec in **approval/allowlist** mode
* approve only the wrapper command
* never grant broad shell access

> OpenClaw versions differ in where approvals live. If your UI doesn’t render “Accounts” sections, you can still operate via Sessions + approvals.

### 6) Add agent instructions (Option 1 command format)

Add a rule to your **main agent instructions** (e.g., in `workspace/AGENTS.md`) to recognize this pattern:

```text
Edit PPTX <file>.pptx: replace "<old>" with "<new>" on slide <n>
Edit PPTX <file>.pptx: replace "<old>" with "<new>" on all slides
```

Behavior:

* extract `file`, `old`, `new`, optional `slide`
* run:

  ```text
  /home/<LINUX_USER>/tools/pptx_edit_safe.sh "<file>.pptx" "<old>" "<new>" [slide]
  ```
* reply with output path and `changes=N`
* refuse any request outside the Desktop folder

Reload or restart OpenClaw after editing instructions.

---

## Usage (Telegram)

Put the PPTX in `Desktop/OpenClaw_PPT`, then send your bot:

All slides:

```text
Edit PPTX Art_Class.pptx: replace "Class" with "Course" on all slides
```

Single slide (1-based):

```text
Edit PPTX Art_Class.pptx: replace "Class" with "Course" on slide 3
```

Output:

* `Art_Class_edited_YYYYMMDD-HHMMSS.pptx`

---

## Security Notes (important)

* Keep the gateway bound to `127.0.0.1` (local only).
* Restrict all edits to a **single dedicated folder** (`OpenClaw_PPT`).
* Allowlist/approve only the wrapper script (not arbitrary shell).
* Rotate tokens if they are ever exposed (Telegram bot token, OpenClaw gateway token).
* Prefer “approval required” for Exec until your workflow is stable.

---

## Troubleshooting

### `ModuleNotFoundError: No module named 'pptx'`

Activate venv and install deps:

```bash
source ~/.venvs/openclaw-ppt/bin/activate
pip install python-pptx
```

### Ubuntu pip error: `externally-managed-environment`

Use a venv (`python3 -m venv ...`) instead of installing into system Python.

### Telegram bot replies “access not configured”

Enable Telegram access by adding your user ID to allowlist (or set DM policy to open) and restart the gateway.

### No reply in Telegram

Check OpenClaw is running:

```bash
openclaw daemon start
openclaw gateway status
```

---

## License

Choose your preferred license (MIT/Apache-2.0/etc.).

```
```

