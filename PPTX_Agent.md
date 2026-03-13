### 1) Create a controlled PPTX folder

#### Create on Windows Desktop:

C:\Users\<YOU>\Desktop\OpenClaw_PPT\

#### WSL path:

/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT/

### Put your input PPTX file here

### 2) Create a Python venv and install dependencies for Ubuntu 24.04

#### Ubuntu 24.04 enforces PEP 668; use a venv:

sudo apt update
sudo apt install -y python3-venv

python3 -m venv ~/.venvs/openclaw-ppt
source ~/.venvs/openclaw-ppt/bin/activate
pip install --upgrade pip
pip install python-pptx
python -c "import pptx; print('pptx ok')"

### 3) Create the PPTX editor tool
#### ~/tools/pptx_edit.py

mkdir -p ~/tools
nano ~/tools/pptx_edit.py
chmod +x ~/tools/pptx_edit.py

#### pptx_edit.py should:
#### accept --in, --outdir, --find, --replace, optional --slide
#### write *_edited_<timestamp>.pptx
#### print output path and changes=N

### 4) Wrapper to enforces security boundary
#### ~/tools/pptx_edit_safe.sh (recommended)
#### only works inside OpenClaw_PPT
#### accepts only a filename (no paths)
#### requires .pptx

nano ~/tools/pptx_edit_safe.sh
chmod +x ~/tools/pptx_edit_safe.sh

#### In the wrapper, set:
PPT_DIR="/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT"
PY="/home/<LINUX_USER>/.venvs/openclaw-ppt/bin/python"
SCRIPT="/home/<LINUX_USER>/tools/pptx_edit.py"

### 5) Manual test
~/tools/pptx_edit_safe.sh Art_Class.pptx Class Course

#### This should create a new edited file in the same folder

### 6) Telegram message

#### Supported command format (natural language)

Edit PPTX <file>.pptx: replace "<old>" with "<new>" on slide <n>
Edit PPTX <file>.pptx: replace "<old>" with "<new>" on all slides

#### Example:

Edit PPTX Art_Class.pptx: replace "Class" with "Course" on all slides

#### OpenClaw replies with an output filename and a change summary

### Security notes (important)
#### Keep the OpenClaw gateway bound to localhost (127.0.0.1) unless you really need remote access
#### Restrict edits to a single dedicated folder (OpenClaw_PPT)
#### Use a safe wrapper script to prevent path traversal and cross-folder access
#### Keep Exec/tool access restricted (approval/allowlist) to only the wrapper command
#### Rotate tokens if exposed (Telegram bot token, OpenClaw dashboard/gateway token)
