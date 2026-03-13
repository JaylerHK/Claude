# OpenClaw (MaxClaw) → Claude Code on Mac Setup Guide

## Overview

This guide explains how to configure **MaxClaw** (running on VPS) to call **Claude Code** on your MacBook Air for code writing and code review tasks in non-interactive mode.

### Architecture

```
┌─────────────────┐         ┌─────────────────┐
│   MaxClaw       │         │   MacBook Air   │
│   (VPS)         │ ──────> │   Claude Code   │
│                 │  SSH    │   (Local)       │
│  - ACP Agent    │         │  -p mode        │
│  - Spawns       │         │  --dangerously-│
│    Claude Code  │         │    skip-perms   │
└─────────────────┘         └─────────────────┘
```

---

## Part 1: Mac Side Setup

### Step 1.1: Install Claude Code on Mac

If not already installed:

```bash
# Install Claude Code CLI
brew install anthropic-cli

# Verify installation
claude --version
```

### Step 1.2: Enable SSH Access on Mac

1. **System Settings → General → Remote Login** → Enable "Remote Login"
2. Allow access for: "All users" or your specific user

Or via terminal:

```bash
# Enable SSH
sudo systemsetup -setremotelogin on

# Or modern macOS
sudo launchctl enable system/com.openssh.sshd
sudo launchctl load -w /System/Library/LaunchDaemons/ssh.plist
```

### Step 1.3: Test Claude Code Non-Interactive Mode

Test locally on your Mac:

```bash
# Basic test with --dangerously-skip-permissions
claude -p --dangerously-skip-permissions "Write a hello world in Python"

# With JSON output for programmatic use
claude --print --output-format=json --dangerously-skip-permissions "What is 2+2?"
```

### Step 1.4: Set Up SSH Key Authentication

```bash
# On Mac: Generate SSH key (if not exists)
ssh-keygen -t ed25519 -C "your@email.com"

# Copy public key to enable passwordless SSH
cat ~/.ssh/id_ed25519.pub >> ~/.ssh/authorized_keys
```

---

## Part 2: VPS (MaxClaw) Side Setup

### Step 2.1: Install ACPX Plugin

On the VPS where MaxClaw runs:

```bash
# Install acpx plugin
openclaw plugins install acpx

# Enable the plugin
openclaw config set plugins.entries.acpx.enabled true
```

### Step 2.2: Configure Permission Mode

```bash
# Set permission mode to approve-all for non-interactive execution
openclaw config set plugins.entries.acpx.config.permissionMode approve-all
openclaw config set plugins.entries.acpx.config.nonInteractivePermissions fail
```

### Step 2.3: Allow Claude Agent

```bash
# Add claude to allowed agents
openclaw config set acp.allowedAgents "[\"pi\", \"claude\", \"codex\", \"opencode\", \"gemini\", \"kimi\"]"

# Enable ACP
openclaw config set acp.enabled true
```

---

## Part 3: SSH Tunnel Configuration

### Option A: SSH Tunnel (Recommended)

MaxClaw connects to your Mac via SSH:

```bash
# From VPS: Test SSH connection to Mac
ssh user@your-mac-ip-or-hostname "echo 'connected'"

# Or use SSH config ~/.ssh/config:
Host my-mac
    HostName 192.168.x.x  # Your Mac's IP or hostname
    User your-username
    IdentityFile ~/.ssh/id_ed25519
    ForwardAgent yes
```

### Option B: Using Claude Code's Built-in Remote

Claude Code has a `/remote-control` command that allows remote sessions:

```bash
# On your Mac: Start Claude Code and enable remote control
claude

# Inside Claude Code:
/remote-control enable
```

Then you can connect from anywhere. However, this is typically for connecting TO the Mac FROM elsewhere, not the other way around.

### Option C: Using acpx with SSH Command

Configure acpx to run Claude Code via SSH:

```json
{
  "plugins": {
    "entries": {
      "acpx": {
        "enabled": true,
        "config": {
          "command": "ssh user@mac-host 'claude'",
          "expectedVersion": "any"
        }
      }
    }
  }
}
```

---

## Part 4: Testing the Integration

### Test 1: Local Mac Test

```bash
# On Mac: Test non-interactive Claude Code
claude -p --dangerously-skip-permissions "Create a file called test.py with: print('hello')"

# Verify file created
cat test.py
```

### Test 2: From VPS via SSH

```bash
# On VPS: Test SSH + Claude Code
ssh user@mac-host "claude -p --dangerously-skip-permissions 'Write hello world to /tmp/test.txt'"

# Verify on Mac
ssh user@mac-host "cat /tmp/test.txt"
```

### Test 3: Via OpenClaw ACP

```bash
# In OpenClaw terminal
/acp spawn claude --mode oneshot

# Or spawn from another session
sessions_spawn runtime="acp" agentId="claude" task="Write a simple Python function"
```

---

## Part 5: Claude Code Command Reference

### Non-Interactive Modes

| Flag | Description |
|------|-------------|
| `-p` | Print mode (non-interactive) |
| `--print` | Same as -p |
| `--output-format=json` | JSON output for parsing |
| `--dangerously-skip-permissions` | Skip permission prompts |
| `--model sonnet\|haiku\|opus` | Select model |
| `--system-prompt "..."` | Custom system prompt |

### Examples

```bash
# Simple prompt
claude -p --dangerously-skip-permissions "What is Python?"

# Code generation
claude -p --dangerously-skip-permissions "Write a FastAPI hello world app"

# Code review
claude -p --dangerously-skip-permissions "Review this code: def add(a,b): return a+b"

# With JSON output
claude --print --output-format=json --dangerously-skip-permissions "Hello"
```

### Exit Codes

| Code | Meaning |
|------|---------|
| 0 | Success |
| 1 | General error |
| 2 | Parse error |
| 3 | Max turns exceeded |

---

## Part 6: Troubleshooting

### Error: "Permission prompt unavailable"

**Cause:** Claude Code is waiting for interactive permission.

**Fix:** Add `--dangerously-skip-permissions` flag.

### Error: "ACP runtime backend is not configured"

**Fix:**
```bash
openclaw plugins install acpx
openclaw config set plugins.entries.acpx.enabled true
/acp doctor
```

### Error: "SSH connection refused"

**Fix:** Ensure Remote Login is enabled on Mac and SSH is running.

### Error: "claude: command not found"

**Fix:** Ensure Claude Code is in PATH on the Mac, or use full path:
```bash
# Find path
which claude

# Use full path in config
```

---

## Part 7: Security Considerations

### ⚠️ Warnings

1. **`--dangerously-skip-permissions`** allows file writes without confirmation
2. Only use in trusted environments
3. Consider using a dedicated workspace directory
4. SSH access should use key-based authentication

### Recommended Security Practices

```bash
# 1. Use SSH keys (not passwords)
# 2. Limit SSH access to specific IP if possible
# 3. Use a dedicated user for OpenClaw on Mac
# 4. Consider using a sandboxed directory

# Create dedicated workspace
mkdir -p ~/openclaw-workspace
chmod 700 ~/openclaw-workspace
```

---

## Summary

| Step | Action | Command |
|------|--------|---------|
| 1 | Enable SSH on Mac | System Settings → Remote Login |
| 2 | Test Claude Code | `claude -p --dangerously-skip-permissions "hi"` |
| 3 | Install acpx on VPS | `openclaw plugins install acpx` |
| 4 | Configure permissions | `openclaw config set plugins.entries.acpx.config.permissionMode approve-all` |
| 5 | Test SSH | `ssh user@mac-host "claude -p --dangerously-skip-permissions 'hi'"` |
| 6 | Spawn from OpenClaw | `/acp spawn claude --mode oneshot` |

---

## Next Steps

After setup, you can:

1. **Code Writing**: Ask MaxClaw to write code → spawns Claude Code on your Mac
2. **Code Review**: Ask MaxClaw to review code → Claude Code analyzes locally
3. **Full Projects**: Delegate larger tasks to Claude Code via ACP

Let me know when you're ready to proceed with the setup!
