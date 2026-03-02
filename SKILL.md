---
name: swift-app-automator
description: Automates macOS Swift applications using the Accessibility API. Use when the user needs to interact with, control, or test a native macOS application built with Swift. Triggers include requests like "automate this Swift app", "control that macOS desktop app", or any task requiring programmatic interaction with a Swift-based GUI.
---

# Swift App Automation

This skill enables the automation of native macOS applications built with Swift (or any native macOS app). It leverages **Peekaboo** (v3), a CLI tool that uses the macOS Accessibility API to inspect and control UI elements.

## Prerequisites

### Installation

```shell
brew tap steipete/tap && brew install peekaboo
```

### Permissions Check

**Always run this first** before any automation task:

```shell
peekaboo permissions
```

Required permissions (grant in System Settings > Privacy & Security):
- **Screen Recording** (Screen & System Audio Recording)
- **Accessibility**

If permissions are missing, run `peekaboo permissions grant` for instructions.

## Core Workflow

Every automation task follows this pattern:

1. **Check Permissions**: Run `peekaboo permissions` to verify access.
2. **Launch App**: Use `open -a "AppName"` or `peekaboo app launch "AppName"`.
3. **Capture UI (See)**: Run `peekaboo see --app AppName --json` to get a snapshot with element IDs.
4. **Interact**: Use element IDs from the snapshot to click, type, scroll, etc.
5. **Verify**: Capture another snapshot to confirm the UI changed as expected.

### Example: Interacting with macOS Calculator

```shell
# 1. Launch the Calculator app
open -a Calculator

# 2. Capture the UI to find element IDs
peekaboo see --app Calculator --json
# Output includes:
#   "snapshot_id": "C7E7D013-...",
#   "ui_elements": [
#     {"id": "elem_10", "role": "button", "description": "1"},
#     {"id": "elem_14", "role": "button", "description": "add"},
#     {"id": "elem_18", "role": "button", "description": "2"},
#     {"id": "elem_22", "role": "button", "description": "equals"},
#   ]

# 3. Extract snapshot ID (use jq or python)
SNAP_ID=$(peekaboo see --app Calculator --json | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# 4. Perform a calculation: 1 + 2 =
peekaboo click --on "elem_10" --snapshot "$SNAP_ID"
peekaboo click --on "elem_14" --snapshot "$SNAP_ID"
peekaboo click --on "elem_18" --snapshot "$SNAP_ID"
peekaboo click --on "elem_22" --snapshot "$SNAP_ID"

# 5. Verify the result
peekaboo see --app Calculator --json
# Check the output to confirm the result.
```

### Example: Typing into an App

```shell
# 1. Capture UI to find the text input element
SNAP_ID=$(peekaboo see --app ChatGPT --json | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# 2. Click the text input field (use element ID from the snapshot)
peekaboo click --on "elem_142" --snapshot "$SNAP_ID"

# 3. Type text (positional argument, NOT --text)
peekaboo type "Hello, world!" --app ChatGPT

# 4. Press Enter to submit
peekaboo press return --app ChatGPT
```

## Essential Commands

### Capture & Inspect

| Command | Description |
|---|---|
| `peekaboo see --app <App> --json` | Capture UI elements with IDs and screenshot |
| `peekaboo see --app <App> --annotate` | Capture with visual annotations on screenshot |
| `peekaboo see --mode screen` | Capture the full screen |

### Click & Interact

| Command | Description |
|---|---|
| `peekaboo click --on <elem_ID> --snapshot <SNAP_ID>` | Click element by ID |
| `peekaboo click --on <elem_ID> --snapshot <SNAP_ID> --double` | Double-click |
| `peekaboo click --on <elem_ID> --snapshot <SNAP_ID> --right` | Right-click |
| `peekaboo click --coords 100,200 --app <App>` | Click at coordinates |

### Type & Keyboard

| Command | Description |
|---|---|
| `peekaboo type "text" --app <App>` | Type text into focused element |
| `peekaboo type "text" --return` | Type text and press Enter |
| `peekaboo type "text" --clear` | Clear field first, then type |
| `peekaboo press return` | Press Enter/Return key |
| `peekaboo press tab --count 3` | Press Tab 3 times |
| `peekaboo press escape` | Press Escape key |
| `peekaboo hotkey "cmd,c"` | Keyboard shortcut (Cmd+C) |
| `peekaboo hotkey "cmd,shift,t"` | Multi-modifier shortcut |

### Scroll

| Command | Description |
|---|---|
| `peekaboo scroll --direction down --amount 5 --app <App>` | Scroll down |
| `peekaboo scroll --direction up --amount 10 --app <App>` | Scroll up |
| `peekaboo scroll --direction down --amount 3 --smooth` | Smooth scroll |

### Paste (Clipboard)

| Command | Description |
|---|---|
| `peekaboo paste "text" --app <App>` | Set clipboard, paste, restore |
| `peekaboo paste --file-path /tmp/image.png --app <App>` | Paste a file |

### Application Control

| Command | Description |
|---|---|
| `peekaboo app launch "AppName"` | Launch an app |
| `peekaboo app launch "AppName" --wait-until-ready` | Launch and wait |
| `peekaboo app quit --app <App>` | Quit an app |
| `peekaboo app relaunch "AppName"` | Restart an app |
| `peekaboo app switch --to <App>` | Switch to an app |
| `peekaboo app list --json` | List running apps |

### Window Management

| Command | Description |
|---|---|
| `peekaboo window list --app <App> --json` | List windows |
| `peekaboo window focus --app <App>` | Bring window to front |
| `peekaboo window move --app <App> --x 100 --y 100` | Move window |
| `peekaboo window resize --app <App> --width 1200 --height 800` | Resize window |
| `peekaboo window minimize --app <App>` | Minimize window |
| `peekaboo window maximize --app <App>` | Maximize window |
| `peekaboo window close --app <App>` | Close window |

### Menu Bar

| Command | Description |
|---|---|
| `peekaboo menu list --app <App>` | List all menu items |
| `peekaboo menu click --app <App> --item "New Window"` | Click a menu item |
| `peekaboo menu click --app <App> --path "Format > Font > Show Fonts"` | Nested menu |

## Important Notes

### Snapshot & Element ID Pattern

The most common mistake is trying to click elements without a valid snapshot. Always follow this pattern:

```shell
# Step 1: Capture → get snapshot_id and element IDs
SNAP_ID=$(peekaboo see --app <App> --json | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# Step 2: Use the snapshot_id when clicking
peekaboo click --on "elem_XX" --snapshot "$SNAP_ID"
```

### Extracting Text from UI Elements

To read the text content of a response or UI state:

```shell
peekaboo see --app <App> --json | python3 -c "
import json, sys
d = json.load(sys.stdin)
for el in d['data']['ui_elements']:
    text = el.get('value', '') or el.get('description', '') or el.get('label', '')
    if text and len(str(text)) > 10:
        print(f\"[{el['id']}] role={el['role']} | {str(text)[:200]}\")
"
```

### Waiting for Content Generation

When interacting with AI apps or slow-loading content, check if generation is still in progress:

```shell
# Poll until generation is complete
peekaboo see --app <App> --json | python3 -c "
import json, sys
d = json.load(sys.stdin)
for el in d['data']['ui_elements']:
    desc = el.get('description', '') + el.get('label', '')
    if 'Thinking' in desc or 'Stop' in desc or '考えています' in desc:
        print('GENERATING')
        sys.exit(0)
print('DONE')
"
```

### Global Options

All commands support these flags:
- `--json` / `-j`: Machine-readable JSON output
- `--app <App>`: Target application by name or bundle ID
- `--verbose` / `-v`: Enable verbose logging

For full command reference, see `references/peekaboo_command_reference.md`.
