# Mobile Access: Control Your Agents From Anywhere

Full CLI access to your development machine from your phone. Survives sleep, wifi-to-cell handoffs, spotty signal, and airplane mode.

## The Stack

```
iPhone (Blink Shell)
  |
  mosh (UDP, handles network transitions)
  |
  Tailscale (encrypted mesh VPN, no port forwarding)
  |
  Mac Mini / Dev Machine
  |
  tmux (persistent terminal sessions)
  |
  Claude Code (running in persistent sessions)
```

## Why Each Layer

| Layer | What it solves |
|-------|---------------|
| **Blink Shell** | Best iOS terminal. Native mosh support. Touch-friendly. |
| **mosh** | UDP-based remote terminal. Survives sleep, wifi/cell switching, high latency. Predicts keystrokes locally so typing feels instant. |
| **Tailscale** | Encrypted mesh network between all your devices. No ports opened on router, no firewall changes, no dynamic DNS. Just works. |
| **tmux** | Keeps terminal sessions alive when you disconnect. Reconnect hours later, everything is exactly where you left it. |

## Server Setup (Mac / Linux)

### 1. Install Tailscale

```bash
# macOS
brew install tailscale
sudo brew services start tailscale
tailscale up

# Linux
curl -fsSL https://tailscale.com/install.sh | sh
sudo systemctl enable --now tailscaled
tailscale up
```

Sign in with your account. Note the Tailscale IP (100.x.x.x).

### 2. Install mosh

```bash
# macOS
brew install mosh

# Ubuntu/Debian
sudo apt install mosh
```

### 3. Install and configure tmux

```bash
brew install tmux  # or apt install tmux
```

Create `~/.tmux.conf`:

```
# Prefix: Ctrl-A (easier on phone keyboards than Ctrl-B)
set -g prefix C-a
unbind C-b
bind C-a send-prefix

# Mouse support (touch scrolling works)
set -g mouse on

# Large scrollback
set -g history-limit 50000

# Windows start at 1
set -g base-index 1

# Split panes with | and -
bind | split-window -h
bind - split-window -v
```

### 4. Enable SSH

```bash
# macOS: System Settings > General > Sharing > Remote Login
# Linux: sudo systemctl enable --now sshd
```

### 5. Start a persistent Claude Code session

```bash
tmux new -s claude
claude  # or open VS Code with Claude Code
```

Detach with `Ctrl-A d`. The session keeps running.

## Phone Setup (iOS)

### 1. Install Tailscale
[App Store: Tailscale](https://apps.apple.com/app/tailscale/id1470499037). Sign in with the same account as your dev machine.

### 2. Install Blink Shell
[App Store: Blink Shell](https://apps.apple.com/app/blink-shell-code/id1594898306). Worth the subscription for mosh support alone.

### 3. Configure the connection

In Blink Shell, create a new host:
- **Alias**: `macmini` (or whatever you want to type)
- **Hostname**: Your machine's Tailscale IP (100.x.x.x)
- **User**: Your username
- **Key**: Generate in Blink, add public key to `~/.ssh/authorized_keys` on your machine
- **Mosh**: Enable, set server path to `/opt/homebrew/bin/mosh-server` (macOS) or `/usr/bin/mosh-server` (Linux)

### 4. Connect

```bash
mosh macmini -- tmux a -t claude
```

That's it. You're looking at your Claude Code session from your phone.

## Daily Workflow

### Before leaving the desk
Make sure Claude Code is running inside a tmux session. That's the only requirement.

### From your phone
Open Blink Shell. Type `mosh macmini -- tmux a -t claude`. You're in.

### Approve a push
Agent asks for approval. You type "ship it" on your phone. Done.

### Disconnect
Close the app. Lock your phone. Walk away. The session persists. mosh reconnects automatically next time you open Blink.

### Multiple sessions
```bash
tmux new -s samaritan    # OSS contribution session
tmux new -s project      # project work session
tmux ls                  # see all sessions
tmux a -t samaritan      # switch to samaritan
```

## Troubleshooting

| Problem | Fix |
|---------|-----|
| Can't connect | Check Tailscale is green on both devices |
| mosh timeout | Verify mosh-server path: `which mosh-server` on the server |
| tmux session gone | Machine rebooted or process crashed. `tmux new -s claude` |
| Typing is laggy | mosh should handle this. If persistent, check Tailscale relay vs direct connection: `tailscale status` |
| Key rejected | Re-add Blink's public key to `~/.ssh/authorized_keys` |

## Android

Same concept, different app. Use [JuiceSSH](https://play.google.com/store/apps/details?id=com.sonelli.juicessh) or [Termux](https://termux.dev/) with mosh installed (`pkg install mosh`).

## Security Notes

- Tailscale encrypts all traffic end-to-end (WireGuard under the hood)
- No ports are opened on your router
- SSH keys only, no password auth
- mosh uses UDP on ports 60000-61000; Tailscale handles this transparently
- Your dev machine is not exposed to the public internet at any point
