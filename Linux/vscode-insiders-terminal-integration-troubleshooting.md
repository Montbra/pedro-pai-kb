# VSCode Insiders Terminal Integration Troubleshooting Guide

## Issue Description
Two main issues were encountered with VSCode Insiders terminal integration:
1. Shell initialization errors with code/code-insiders
2. Terminal output capture behavior

## Shell Integration Error

### Symptoms
- Error messages during shell initialization:
```
/home/henrique/.zshrc:139: command not found: code
/home/henrique/.zshrc:.:139: no such file or directory:
```

### Root Cause
- Shell integration in .zshrc was trying to use `code` command instead of `code-insiders`
- The system had VSCode Insiders installed (`code-insiders`) but not regular VSCode

### Solution
Modified the VS Code shell integration section in .zshrc:

Original code:
```bash
if [[ "$TERM_PROGRAM" == "vscode" ]]; then
  . "$(code --locate-shell-integration-path zsh)"
elif [[ "$TERM_PROGRAM" == "vscode-insiders" ]]; then
  . "$(code-insiders --locate-shell-integration-path zsh)"
```

Fixed version:
```bash
if [[ "$TERM_PROGRAM" == "vscode" || "$TERM_PROGRAM" == "vscode-insiders" ]]; then
  . "$(code-insiders --locate-shell-integration-path zsh)"
fi
```

### Resolution Steps
1. Created backup of .zshrc: `cp /home/henrique/.zshrc /home/henrique/.zshrc.bak`
2. Modified shell integration to use code-insiders
3. Reloaded shell configuration: `source /home/henrique/.zshrc`

## Terminal Behavior Notes

### Command Execution
- Each command is executed in a new terminal instance
- This is normal and expected behavior
- Benefits:
  - Provides clean isolation between commands
  - Prevents interference between operations
  - Allows long-running commands to continue independently
  - Maintains separate environment for each command

### Output Capture
- Commands show "Command executed" message
- Terminal output (both stdout and stderr) is properly captured
- Multi-line output is supported
- Example output format:
```
Command executed.
Output:
[actual command output here]
```

## Prevention
- Always use `code-insiders` instead of `code` in shell scripts and configurations
- Remember that each command runs in a new terminal - structure scripts accordingly
- Keep backup of working shell configurations

## References
- [VSCode Insiders Documentation](https://code.visualstudio.com/insiders/)
- [Shell Integration Documentation](https://code.visualstudio.com/docs/terminal/shell-integration)
