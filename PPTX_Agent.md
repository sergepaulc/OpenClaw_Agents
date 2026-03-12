## 1) Create a controlled PPTX folder

### Create on Windows Desktop:

C:\Users\<YOU>\Desktop\OpenClaw_PPT\

### WSL path:

/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT/

## Put your input PPTX file here

# 2) Create a Python venv and install dependencies for Ubuntu 24.04

# Ubuntu 24.04 enforces PEP 668; use a venv:

sudo apt update
sudo apt install -y python3-venv

python3 -m venv ~/.venvs/openclaw-ppt
source ~/.venvs/openclaw-ppt/bin/activate
pip install --upgrade pip
pip install python-pptx
python -c "import pptx; print('pptx ok')"

# 3) Create the PPTX editor tool
# ~/tools/pptx_edit.py

mkdir -p ~/tools
nano ~/tools/pptx_edit.py
chmod +x ~/tools/pptx_edit.py

# pptx_edit.py should:
# accept --in, --outdir, --find, --replace, optional --slide
# write *_edited_<timestamp>.pptx
# print output path and changes=N

# Wrapper to enforces security boundary:
# ~/tools/pptx_edit_safe.sh (recommended)
# only works inside OpenClaw_PPT
# accepts only a filename (no paths)
# requires .pptx

nano ~/tools/pptx_edit_safe.sh
chmod +x ~/tools/pptx_edit_safe.sh

# In the wrapper, set:
PPT_DIR="/mnt/c/Users/<YOU>/Desktop/OpenClaw_PPT"
PY="/home/<LINUX_USER>/.venvs/openclaw-ppt/bin/python"
SCRIPT="/home/<LINUX_USER>/tools/pptx_edit.py"
