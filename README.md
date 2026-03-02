# swift-app-automator

A [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code/skills) that enables AI agents to automate native macOS desktop applications using the Accessibility API via [Peekaboo](https://github.com/steipete/Peekaboo) CLI.

## What It Does

This skill teaches Claude Code how to **see, click, type, scroll, and control** any native macOS application — Calculator, ChatGPT, Finder, Notes, or any Swift/AppKit/Electron app.

### Capabilities

- **Capture UI** — snapshot any app's interface and get structured element IDs
- **Click elements** — single, double, or right-click by element ID or coordinates
- **Type text** — type into any focused input field with human-like cadence
- **Keyboard shortcuts** — press keys, hotkeys, and key sequences
- **Scroll** — scroll in any direction with configurable amount
- **App control** — launch, quit, relaunch, switch between apps
- **Window management** — move, resize, minimize, maximize, close windows
- **Menu bar** — click menu items and navigate nested menus
- **Clipboard** — paste text, images, or files via clipboard

## Requirements

- **macOS 15+** (Sequoia or later)
- **Homebrew** installed
- **Accessibility** and **Screen Recording** permissions granted to your terminal

## Installation

### 1. Install the Skill

```shell
npx skills add yutanoguchi/swift-app-automator
```

Or manually copy the skill directory into your project's `.claude/skills/` folder.

### 2. Install Peekaboo

```shell
brew tap steipete/tap && brew install peekaboo
```

### 3. Grant Permissions

Open **System Settings > Privacy & Security** and enable:
- **Accessibility** — for your terminal app
- **Screen & System Audio Recording** — for your terminal app

Verify with:

```shell
peekaboo permissions
```

## Quick Start

Once installed, simply ask Claude Code to interact with any macOS app:

```
> Open Calculator and calculate 42 * 7

> Type "Hello" into the Notes app

> Take a screenshot of Safari and describe what's on the page

> Click the "New Document" menu item in TextEdit
```

## How It Works

The skill follows a **See → Interact → Verify** loop:

```
1. peekaboo see --app <App> --json     → Capture UI elements with IDs
2. peekaboo click --on <elem_ID> ...   → Interact with elements
3. peekaboo see --app <App> --json     → Verify the result
```

### Example: Automating Calculator

```shell
# Launch
open -a Calculator

# Capture UI → get snapshot_id and element IDs
SNAP_ID=$(peekaboo see --app Calculator --json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# Click: 1 + 2 =
peekaboo click --on "elem_10" --snapshot "$SNAP_ID"
peekaboo click --on "elem_14" --snapshot "$SNAP_ID"
peekaboo click --on "elem_18" --snapshot "$SNAP_ID"
peekaboo click --on "elem_22" --snapshot "$SNAP_ID"

# Verify
peekaboo see --app Calculator --json
```

### Example: Typing into ChatGPT

```shell
# Capture UI
SNAP_ID=$(peekaboo see --app ChatGPT --json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# Click the text input field
peekaboo click --on "elem_142" --snapshot "$SNAP_ID"

# Type and submit
peekaboo type "Hello, world!" --app ChatGPT
peekaboo press return --app ChatGPT
```

## Command Reference

| Category | Commands |
|---|---|
| Capture | `see` |
| Interaction | `click`, `type`, `press`, `hotkey`, `scroll`, `paste`, `drag`, `move`, `swipe` |
| System | `app`, `window`, `menu`, `menubar`, `dock`, `clipboard`, `dialog`, `space` |

See [`references/peekaboo_command_reference.md`](references/peekaboo_command_reference.md) for the full command reference.

## Project Structure

```
swift-app-automator/
  SKILL.md                                  # Skill definition (loaded by Claude Code)
  references/
    peekaboo_command_reference.md            # Full Peekaboo v3 CLI reference
  README.md                                 # This file
  LICENSE                                   # MIT License
```

## Powered By

- [Peekaboo](https://github.com/steipete/Peekaboo) — macOS CLI for GUI automation via Accessibility API
- [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills) — extensible skill system for Claude Code

## License

MIT License. See [LICENSE](LICENSE) for details.
