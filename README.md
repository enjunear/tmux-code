# tmux-code

A VS Code-inspired terminal IDE layout using tmux.

## Disclaimer

This is a personal side project built for fun. There is no intent to actively maintain it or for it to be genuinely useful to anyone. Use at your own risk. No warranties, no support, no guarantees.

## What is it?

A shell script that creates a tmux session with a VS Code-like pane layout:

```
|---------------------------------------------------------------|
| FILE EXPLORER |  EDITOR (vim)              | AI ASSISTANT     |
| [lf/ranger]   |                            | (claude/aider)   |
|               |                            |                  |
|               |----------------------------|                  |
|               |  TERMINAL (bash)           |                  |
|---------------------------------------------------------------|
```

## Usage

```bash
tmux-code [path]              # Open a project
tmux-code --agent aider       # Use a different AI tool
tmux-code --no-agent          # Skip the AI panel
```

## Requirements

- tmux
- vim or nvim
- lf, ranger, or tree (for file explorer)
- claude CLI (optional, for AI panel)

## License

Do whatever you want with it.
