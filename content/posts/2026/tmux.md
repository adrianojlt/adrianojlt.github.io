
+++
title = 'Tmux'
date = '2026-03-22'
draft = false
tags = ['dev']
#author = 'adriano'
header_image = "/images/tmux.png"
+++

I already heard about tmux but never tried it. Now with fast growing pace of AI CLI tools like **Claude Code** and **OpenCode**, tmux seems like it could be helpful for managing multiple terminals. This article will be more of a way to have a quick reference on how i use tmux. There are already plenty of articles about tmux.

## Quick Overview
Tmux is a terminal multiplexer to facilitate your life managing multiple terminal **windows**. How do you manage tabs within your browser? You have one or more instances of a browser (windows), and you create tabs inside each instance, right? you can also group tabs by topic if you want.

With tmux, is the same but for terminals, with a different nomenclature. You have **sessions** (like browser window instances) that keep running even if you close iTerm2 (terminal emulator I use on my mac), there is a concept of **attachment** where only one session can be attached at a time.

This is not an extensive overview of tmux, there are already plenty of tutorials with good info about that. This intents to be more of a quick reference on what and how to use tmux.


## Core ideas

- **Session** = workspace
- **Window** = tab inside a session
- **Pane** = split inside a window
- **Attached** = a terminal is currently connected to that session
- **Detached** = the session is still running, but no terminal is connected to it
- **Prefix** = `Ctrl+b`
- **Command Mode** = `Ctrl+b :`

## Sessions

| Command | Description |
|---|---|
| `tmux ls` | List sessions |
| `tmux a -t 0` | Attach to session `0` |
| `tmux a -t session_name` | Attach to session `session_name` |
| `tmux new -s mine` | Create and attach to session `mine` |
| `tmux new -s mine -d` | Create session `mine` in background |
| `tmux new -As mine` | Attach if exists, otherwise create |
| `tmux kill-ses -t mine` | Kill session `mine` |
| `tmux kill-server` | Kill Server |
| `tmux rename-session -t oldname newname` | Rename a session |

### Session shortcuts

| Shortcut | Description |
|---|---|
| `Ctrl+b d` | Detach from current session |
| `Ctrl+b s` | Open session picker |
| `Ctrl+b $` | Rename current session |

---

## Windows

| Shortcut | Description |
|---|---|
| `Ctrl+b c` | Create new window |
| `Ctrl+b n` | Next window |
| `Ctrl+b p` | Previous window |
| `Ctrl+b 0-9` | Jump to window number |
| `Ctrl+b w` | List windows |
| `Ctrl+b ,` | Rename current window |
| `Ctrl+b &` | Close current window |
| `Ctrl+b .` | Move window to another index |

### Reorder windows (command mode)

| Command | Description |
|---|---|
| `move-window -t 3` | Move current window to position 3 |
| `swap-window -t 3` | Swap current window with position 3 |

---
## Panes

| Shortcut | Description |
|---|---|
| `Ctrl+b "` | Split horizontally |
| `Ctrl+b %` | Split vertically |
| `Ctrl+b ←→↑↓` | Move between panes |
| `Ctrl+b z` | Toggle zoom |
| `Ctrl+b x` | Close current pane |
| `Ctrl+b q` | Show pane numbers |

---
## Resize panes

### macOS keyboard

```
Ctrl+b then Ctrl+↑↓←→
```

### Command mode

| Command | Description |
|---|---|
| `resize-pane -U 10` | Resize up |
| `resize-pane -D 10` | Resize down |
| `resize-pane -L 10` | Resize left |
| `resize-pane -R 10` | Resize right |

---

## Command mode

Open with `Ctrl+b :`, then type a command:

```
new-session -s mine
move-window -t 3
swap-window -t 3
resize-pane -R 10
setw synchronize-panes on
setw synchronize-panes off
```

---

## Synchronize typing to all panes

```
Ctrl+b :
setw synchronize-panes on   # enable
setw synchronize-panes off  # disable
```

Optional toggle in `~/.tmux.conf`:

```bash
bind e set-window-option synchronize-panes
```

Use with `Ctrl+b e`.

---

## Fast window switching (no prefix)

To have this you first need to have all these panel numbers.
To remove the panel `0`, go to that panel then: `Ctrl+b x`

Add to `~/.tmux.conf`:

```bash
bind -n M-1 select-window -t 1
bind -n M-2 select-window -t 2
bind -n M-3 select-window -t 3
```

- `M-1` = `Option+1` on Mac
- `-n` = no prefix needed
- `Command` key is NOT supported in tmux on macOS

---

## Config file

```bash
nano ~/.tmux.conf                  # edit config
tmux source-file ~/.tmux.conf      # reload config
```

---

## Closing iTerm2

Closing iTerm2 **detaches** the session — it does NOT kill it.
Reattach anytime with `tmux a -t session_name`.

---

## Quick reference — TLDR

```
tmux ls
tmux new -As work
tmux a -t work
tmux new -s mine -d
tmux rename-session -t oldname newname
tmux kill-ses -t mine
tmux source-file ~/.tmux.conf

Ctrl+b d          detach
Ctrl+b s          session picker
Ctrl+b $          rename session
Ctrl+b c          new window
Ctrl+b n / p      next/prev window
Ctrl+b 0-9        jump to window
Ctrl+b w          list windows
Ctrl+b ,          rename window
Ctrl+b &          close window
Ctrl+b .          move window
Ctrl+b "          split horizontal
Ctrl+b %          split vertical
Ctrl+b ←→↑↓       navigate panes
Ctrl+b z          zoom pane
Ctrl+b x          close pane
Ctrl+b :          command mode
```
