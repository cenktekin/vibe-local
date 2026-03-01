# Vibe-Coder (Local Optimized Version)

> **Free AI Coding Agent — Offline, Local, Open Source**
>
> Originally created by [ochyai/vibe-local](https://github.com/ochyai/vibe-local). This fork contains additional security and performance patches for enhanced reliability and scale.

## Overview

A single-file Python agent that operates efficiently in your terminal. It connects directly to Ollama to provide an completely offline, lightning-fast coding agent. 

### What's new in this Fork?
This repository contains exclusive local optimizations that make the agent significantly faster and more secure:

- **Autonomy Sandboxing (Defense in Depth)**: Supports `vibe-coder.toml` configurations for strict bounds.
  - `workspace_only`: Blocks the agent from accessing or editing files outside the project root.
  - `allowed_commands` & `forbidden_paths`: Whitelist/blacklist control over the terminal execution.
  - `network_access`: Blocks the terminal from downloading payloads via `curl/wget` when disabled.
  - `require_approval_for`: Aggressive interception that always asks for user permission on destructive commands (like `rm` or `format`), even if auto-approve (`-y`) is on.
- **NumPy RAG Acceleration**: Scans codebases using C-level tensor calculations instead of native Python loops. Automatically falls back to standard python math if `numpy` is missing.
- **Token Caching**: Uses `functools.lru_cache` to prevent redundant HTTP requests to Ollama's API for token counting. Dramatically speeds up context re-calculation.
- **OOM Prevention & Security**: Hardened file access. `ReadTool` size boundary reduced to 2MB to prevent context exhaustion. WebFetchTool includes SSRF loopback protections.
- **Standalone Installers**: The customized `install.ps1/sh` wrappers inherently install Python dependencies (`requirements.txt`) and do not auto-overwrite themselves with upstream logic.

---

## 🚀 Installation & Usage

**1. Install Dependencies (for RAG acceleration):**
```bash
pip install -r requirements.txt
```

**2. Run the Agent:**
```bash
# Interactive mode (chat with AI while coding)
vibe-local

# One-shot (ask once)
vibe-local -p "Create a python script"

# Specify model manually
vibe-local --model qwen3:8b

# Auto-approve mode
vibe-local -y
```

---

## 🛠️ CLI Reference

### Environment Flags
| Flag | Short | Description |
|------|-------|-------------|
| `--prompt` | `-p` | One-shot prompt (non-interactive) |
| `--model` | `-m` | Specify Ollama model name |
| `--yes` | `-y` | Auto-approve all tool calls |
| `--debug` | | Enable debug logging |
| `--resume` | | Resume last session |

### Interactive Commands (Type inside the agent)
| Command | Description |
|---------|-------------|
| `/help` | Show commands |
| `/exit`, `/quit`, `/q` | Exit (auto-saves) |
| `/clear` | Clear history |
| `/model <name>` | Switch model |
| `/status` | Session info |
| `/save` | Save session |
| `/compact` | Compress history |
| `/plan` | Plan mode (read-only analysis) |
| `/approve`, `/act` | Switch to act mode (execute plan) |
| `/checkpoint` | Save git checkpoint |
| `/rollback` | Rollback to last checkpoint |
| `/autotest` | Toggle auto lint+test after edits |
| `"""` | Multi-line input |

---

## ⚙️ Configuration
The agent stores its configuration at `~/.config/vibe-local/config`.
Example:
```bash
# ~/.config/vibe-local/config
MODEL="qwen3:8b"
SIDECAR_MODEL="qwen3:1.7b"
OLLAMA_HOST="http://localhost:11434"
```
