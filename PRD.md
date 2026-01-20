# tmux-code PRD

> A VS Code-inspired terminal IDE using tmux

## Overview

`tmux-code` is a shell script that creates a tmux session with a VS Code-like pane layout, combining file exploration, editing, terminal, git, and AI assistance in a unified terminal experience.

## Goals

1. **Familiar layout** - Mimic VS Code's iconic 3-panel design
2. **Zero dependencies** - Work with standard tools (tmux, vim, bash, lf/ranger)
3. **Quick startup** - Single command to open a project
4. **Keyboard-driven** - Vim-friendly keybindings (scheme TBD)

## Layout

**With AI agent (3-column):**
```
|---------------------------------------------------------------|
| LEFT PANEL    |  EDITOR (vim)              | AI ASSISTANT     |
| [lf/ranger]   |                            | (claude/aider)   |
|   15%         |         50%                |      35%         |
|               |                            |                  |
|               |                            |                  |
|               |----------------------------|                  |
|               |  TERMINAL (bash)           |                  |
|               |                            |                  |
|---------------------------------------------------------------|
```

**Without AI agent (2-column fallback):**
```
|-----------------------------------------------|
| LEFT PANEL    |  EDITOR (vim)                 |
| [lf/ranger]   |                               |
|   20%         |         80%                   |
|               |                               |
|               |                               |
|               |-------------------------------|
|               |  TERMINAL (bash)              |
|               |                               |
|-----------------------------------------------|
```

### Left Panel Modes (tabbed)

Toggle between two modes with a keybinding:

**Explorer Mode (default)**
```
┌─────────────┐
│  lf/ranger  │
│  (file mgr) │
│             │
└─────────────┘
```

**Git Mode**
```
┌─────────────┐
│ git status  │
│ --short     │
├─────────────┤
│ git log     │
│ --oneline   │
└─────────────┘
```

## Features

### MVP (v0.1)

1. **Session Management**
   - Session name: `code-<workspace>` (folder name or git repo name)
   - On launch:
     - If session exists → prompt: attach / kill & recreate / cancel
     - If no session → create new

2. **Layout Creation**
   - 3 columns: 15% / 50% / 35%
   - Editor/Terminal split: 70% / 30% vertical
   - Left panel: single pane (explorer mode by default)

3. **Working Directory**
   - Accept optional path argument: `tmux-code [path]`
   - Default to current directory
   - All panes start in the project root

4. **Default Commands**
   - Left panel: `lf` or `ranger` (interactive file manager, falls back to `lf` → `ranger` → `tree`)
   - Editor: `vim .` (or `nvim` if available)
   - Terminal: `bash` (interactive shell)
   - AI: configurable via `--agent` flag

5. **AI Panel Behavior**
   - `--agent <cmd>` flag to specify AI tool (e.g., `--agent aider`, `--agent "claude --model sonnet"`)
   - If no `--agent` provided: auto-detect `claude` in PATH
   - If `claude` not found and no `--agent`: **skip AI panel entirely** (2-column layout)

### v0.2 - Enhanced Navigation

6. **Left Panel Modes**
   - Keybinding to toggle explorer ↔ git mode
   - Git mode shows split: `git status --short` / `git log --oneline -20`

7. **Keybindings File** *(TBD)*
   - Keybinding scheme undecided - need to avoid conflicts with tmux prefix and vim
   - Leaning toward vim-style bindings rather than VS Code
   - Will define specific bindings during implementation
   - Core needs: pane focus switching, left panel mode toggle, terminal toggle

### v0.3 - Polish

8. **Status Bar**
   - Show current mode, git branch, session name
   - Custom tmux status line styling

9. **Smart Defaults**
   - Detect editor preference (`$EDITOR`, nvim, vim, nano)
   - Detect AI tool (claude, aider, etc.)

## CLI Interface

```
tmux-code [OPTIONS] [PATH]

Arguments:
  PATH                    Project directory (default: current directory)

Options:
  --agent <command>       AI assistant command (default: auto-detect claude)
  --no-agent              Disable AI panel entirely
  --new                   Force new session (skip attach prompt)
  --help                  Show help message
```

## Usage Examples

```bash
# Open current directory (auto-detects claude if installed)
tmux-code

# Open specific project
tmux-code ~/projects/my-app

# Use a different AI agent
tmux-code --agent aider

# Use aider with specific flags
tmux-code --agent "aider --model gpt-4"

# No AI panel (explicit)
tmux-code --no-agent

# Force recreate session (skip prompt)
tmux-code --new
```

## Technical Design

### Script Structure

**Single self-contained script** (`tmux-code`):
- All functions embedded in one file
- Keybindings written to `~/.tmux-code.conf` on first run
- No external dependencies beyond tmux + standard tools
- Can be installed via: `curl -o ~/.local/bin/tmux-code <url> && chmod +x ~/.local/bin/tmux-code`

### Session Naming Logic

```bash
get_workspace_name() {
    # Try git repo name first
    git_name=$(git rev-parse --show-toplevel 2>/dev/null | xargs basename)
    if [[ -n "$git_name" ]]; then
        echo "$git_name"
        return
    fi
    # Fall back to directory name
    basename "$(pwd)"
}
# Result: code-tmux-code, code-my-project, etc.
```

### Pane Layout Algorithm

**3-column (with AI):**
```
1. Create session with first window
2. Split vertically: left (15%) | rest (85%)
3. Select right pane, split vertically: middle (59%) | right (41%)
   - 59% of 85% ≈ 50% total, 41% of 85% ≈ 35% total
4. Select middle pane, split horizontally: editor (70%) | terminal (30%)
5. Send commands to each pane
```

**2-column (no AI):**
```
1. Create session with first window
2. Split vertically: left (20%) | rest (80%)
3. Select right pane, split horizontally: editor (70%) | terminal (30%)
4. Send commands to each pane
```

## Decisions Made

| Question | Decision |
|----------|----------|
| File explorer | Interactive (`lf` preferred, fallback to `ranger`, then `tree`) |
| AI panel fallback | `--agent` flag for custom; auto-detect `claude`; skip pane if neither |
| Distribution | Single self-contained bash script |

## Open Questions

1. **Keybinding scheme** - Need to design bindings that don't conflict with tmux prefix or vim. Leaning vim-style. Will finalize during implementation.

2. **Editor tabs** - How to handle vim tabs in the narrow/wide layout? Trust user's vim config?

3. **lf/ranger config** - Should we provide recommended config for opening files in the editor pane? (Needs IPC)

## Success Metrics

- Script works on fresh Ubuntu/macOS with tmux installed
- Layout matches mockup within 5% margin
- Session attach/recreate flow works smoothly
- Keybindings feel natural to vim/tmux users

## Out of Scope (for now)

- Plugin system
- Theme customization
- Multiple windows/workspaces
- File synchronization
- LSP integration (that's vim's job)
