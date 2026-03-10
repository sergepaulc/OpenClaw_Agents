# OpenClaw Agents

## OpenClaw PPTX Editor 

Edit `.pptx` files automatically from Telegram bots using OpenClaw on Windows.  

No PowerPoint UI automation: we edit the PPTX file directly using `python-pptx`.

## How does it work?

You send a message to your Telegram bot like:
pptx Art_Class.pptx | find=Class | replace=Course

OpenClaw receives it and runs a safe, allowlisted command that:
- only operates inside a single folder (`Desktop/OpenClaw_PPT`)
- never overwrites the original file
- writes a new file: `*_edited_<timestamp>.pptx`

## Architecture

- Telegram Bot: chat interface
- OpenClaw Gateway (WSL Ubuntu): receives messages, routes tasks
- Tool: a safe wrapper script calls a Python PPTX editor
- PPTX Editor: `python-pptx`

## Requirements

- Windows 10/11 with WSL
- Ubuntu 24.04 (WSL)
- OpenClaw installed and connected to Telegram + an LLM provider (Anthropic/OpenAI)
- Python 3 + `python-venv`

## Licensing

This project is licensed under the terms of the MIT license.
