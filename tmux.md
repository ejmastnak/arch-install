Minimal notes for using tmux to have instructions and a shell side-by-side.

```bash
sudo pacman -S tmux

# Start and attach to a new session
tmux new
tmux new -s foo  # start a named session

# Show existing tmux sessions
tmux ls

# Attach to session foo
tmux attach -t foo

# Exit the current session
exit  # or just Ctrl-D
```

Minimal commands:

- Prefix: `<Ctrl-b>` (by default)
- `<prefix> %`: split current window into two panes
- `<prefix> <Up>|<Down>|<Left>|<Right>`: move between panes
- `<prefix> x`: close current pane
