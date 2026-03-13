# OpenClaw Agents

## OpenClaw PPTX Editor 

Edit `.pptx` files automatically from a Telegram bot using OpenClaw on an HP OmniBook Windows machine.

The agent edit the PPTX file directly using `python-pptx`.

## How does it work?

You send a message to your Telegram bot like:
"YourPresentation.pptx | find=Word | replace=NewWord"

OpenClaw receives it and runs a safe, allowlisted command that:
- Only operates inside a single folder (`Desktop/OpenClaw_PPT`)
- Never overwrites the original file
- Writes a new file: `*_edited_<timestamp>.pptx`
- Reply in Telegram with:
  - Output filename
  - Changes summary (including slide or all-slides)

## Architecture

- Telegram Bot: chat interface
- OpenClaw Gateway (WSL Ubuntu): receives messages, routes tasks
- Tool: a safe wrapper script that calls a Python PPTX editor
- PPTX Editor: `python-pptx`

## Requirements

- Windows 10/11 with WSL
- Ubuntu 24.04 (WSL)
- OpenClaw installed and connected to Telegram + an LLM provider (Anthropic/OpenAI API key)
- Python 3 + `python-venv`

## Licensing

This project is licensed under the terms of the MIT license.
