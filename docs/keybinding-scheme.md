# tmux-code Keybinding Scheme

## Design Principles

1. **Vim-friendly**: Use hjkl for navigation, common vim-style patterns
2. **Prefix-based**: All bindings use tmux prefix (default Ctrl-b) to avoid conflicts
3. **Mnemonic**: Bindings should be easy to remember
4. **Non-destructive**: Avoid overriding essential tmux defaults

## Keybinding Scheme

All bindings use the tmux prefix (default: `Ctrl-b`)

### Pane Navigation (vim-style hjkl)

| Binding | Action | Description |
|---------|--------|-------------|
| `prefix + h` | select-pane -L | Move focus left |
| `prefix + j` | select-pane -D | Move focus down |
| `prefix + k` | select-pane -U | Move focus up |
| `prefix + l` | select-pane -R | Move focus right |

### Left Panel Mode Toggle

| Binding | Action | Description |
|---------|--------|-------------|
| `prefix + g` | toggle_left_panel_mode | Toggle between explorer/git modes |

The `g` binding is mnemonic for "git" (toggle to/from git mode).

### Quick Pane Focus

| Binding | Action | Description |
|---------|--------|-------------|
| `prefix + e` | select-pane -t 0 | Focus explorer (pane 0) |
| `prefix + m` | select-pane -t 1 | Focus main editor (pane 1) |
| `prefix + t` | select-pane -t 2 | Focus terminal (pane 2) |
| `prefix + a` | select-pane -t 3 | Focus AI agent (pane 3, if exists) |

Mnemonics: **e**xplorer, **m**ain editor, **t**erminal, **a**i agent

### Pane Resizing (vim-style with Shift)

| Binding | Action | Description |
|---------|--------|-------------|
| `prefix + H` | resize-pane -L 5 | Resize pane left |
| `prefix + J` | resize-pane -D 5 | Resize pane down |
| `prefix + K` | resize-pane -U 5 | Resize pane up |
| `prefix + L` | resize-pane -R 5 | Resize pane right |

## Implementation Notes

### Keybindings File Location
- Primary: `~/.config/tmux-code/bindings.conf`
- Fallback: `~/.tmux-code-bindings.conf`

### Integration with tmux
The keybindings file is sourced by tmux-code when creating a session:
```bash
tmux source-file ~/.config/tmux-code/bindings.conf
```

### Mode Toggle Implementation
The `g` binding calls the `toggle_left_panel_mode` function from tmux-code:
```bash
bind-key g run-shell "tmux-code toggle-mode"
```

This requires adding a `toggle-mode` subcommand to the tmux-code script.

## Conflicts Avoided

The following default tmux bindings are preserved:
- `prefix + c` - Create new window (not overridden)
- `prefix + n` - Next window (not overridden)
- `prefix + p` - Previous window (not overridden)
- `prefix + d` - Detach (not overridden)
- `prefix + :` - Command prompt (not overridden)
- `prefix + [` - Copy mode (not overridden)
- `prefix + ]` - Paste (not overridden)
- `prefix + ?` - Help (not overridden)

Note: `prefix + l` conflicts with "last-window" in default tmux. Since vim-style
navigation is more frequently used, we override this. Users can still use
`prefix + ;` for "last-pane" functionality.

## Future Considerations

- `prefix + /` - Could be used for fuzzy file finder
- `prefix + f` - Could be used for find in files
- `prefix + b` - Could be used for buffer/tab management
