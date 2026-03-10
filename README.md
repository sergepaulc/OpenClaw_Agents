# OpenClaw Agents



## Editing Powerpoint presentations

Here’s a short, GitHub-ready `README.md` you can drop into a repo.

```markdown
# OpenClaw PPTX Editor (WSL + Telegram)

Edit `.pptx` files **automatically from Telegram** using **OpenClaw** on Windows (via WSL2 + Ubuntu).  
No PowerPoint UI automation: we edit the PPTX file directly using `python-pptx`.

## What this does

You send a message to your Telegram bot like:

```

pptx Art_Class.pptx | find=Class | replace=Course

````

OpenClaw receives it and runs a **safe, allowlisted** command that:
- only operates inside a single folder (`Desktop/OpenClaw_PPT`)
- never overwrites the original file
- writes a new file: `*_edited_<timestamp>.pptx`

## Architecture

- **Telegram Bot**: chat interface
- **OpenClaw Gateway** (WSL Ubuntu): receives messages, routes tasks
- **Tool**: a safe wrapper script calls a Python PPTX editor
- **PPTX Editor**: `python-pptx` modifies slides and saves a new deck

## Requirements

- Windows 10/11 with **WSL2**
- Ubuntu 24.04 (WSL)
- OpenClaw installed and connected to Telegram + an LLM provider (Anthropic/OpenAI)
- Python 3 + `python-venv`

## Folder layout (Windows)

Create a dedicated folder for input/output PPTX files:

- `C:\Users\<YOU>\Desktop\OpenClaw_PPT\`

WSL path:

- `/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT`

## Setup

### 1) Create a Python virtual environment (WSL)

```bash
sudo apt update
sudo apt install -y python3-venv

python3 -m venv ~/.venvs/openclaw-ppt
source ~/.venvs/openclaw-ppt/bin/activate
pip install --upgrade pip
pip install python-pptx
python -c "import pptx; print('pptx ok')"
````

### 2) Add the PPTX edit script

Save as:

* `~/tools/pptx_edit.py`

(Example script supports `find/replace` across all slides or a specific slide.)

Run:

```bash
chmod +x ~/tools/pptx_edit.py
python ~/tools/pptx_edit.py --help
```

### 3) Add a safe wrapper script (recommended)

Save as:

* `~/tools/pptx_edit_safe.sh`

This wrapper enforces:

* **only** files in `/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT`
* **only** `.pptx`
* rejects path traversal
* always outputs into the same folder

Make executable:

```bash
chmod +x ~/tools/pptx_edit_safe.sh
```

Test manually:

```bash
PPT_DIR="/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT"
~/.venvs/openclaw-ppt/bin/python ~/tools/pptx_edit.py --in "$PPT_DIR/Art_Class.pptx" --outdir "$PPT_DIR" --find "Class" --replace "Course"
~/tools/pptx_edit_safe.sh Art_Class.pptx Class Course
```

### 4) Wire into OpenClaw (Exec tool + approvals)

Configure OpenClaw so it can run **only**:

```
/home/<YOU>/tools/pptx_edit_safe.sh
```

Recommended:

* keep Exec in **approval/allowlist** mode
* approve only the wrapper command
* never grant broad shell access

> Note: OpenClaw UI/config may differ by version. The safest pattern is “approval required” + allowlist the wrapper script.

## Usage (Telegram)

Put the PPTX in `Desktop/OpenClaw_PPT`, then send your bot:

```
pptx Art_Class.pptx | find=Class | replace=Course
```

Optional: target a single slide (1-based):

```
pptx Art_Class.pptx | find=Class | replace=Course | slide=1
```

The output file will appear in the same folder:

* `Art_Class_edited_YYYYMMDD-HHMMSS.pptx`

## Security Notes

* Keep the gateway bound to `127.0.0.1` (local only).
* Store PPTX files in a dedicated folder and restrict tools to that folder.
* Never paste Telegram bot tokens or OpenClaw gateway tokens into issues/PRs.
* Prefer “approval required” for Exec until your workflow is stable.

## Troubleshooting

* **ModuleNotFoundError: pptx**
  Activate venv and install dependencies:

  ```bash
  source ~/.venvs/openclaw-ppt/bin/activate
  pip install python-pptx
  ```

* **Ubuntu pip: externally-managed-environment**
  Use a venv (`python3 -m venv ...`) instead of system pip.

* **No reply in Telegram**
  Ensure OpenClaw gateway is running:

  ```bash
  openclaw daemon start
  openclaw gateway status
  ```

## License

MIT
```

