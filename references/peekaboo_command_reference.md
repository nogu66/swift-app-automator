# Peekaboo v3 Command Reference

Full command reference for the Peekaboo CLI (v3.x). Run `peekaboo <command> --help` for the latest options.

## Command Overview

| Category | Commands |
|---|---|
| **Core** | `see`, `click`, `type`, `press`, `hotkey`, `scroll`, `paste`, `drag`, `move`, `swipe` |
| **System** | `app`, `window`, `menu`, `menubar`, `dock`, `clipboard`, `dialog`, `space`, `open` |
| **Utility** | `permissions`, `list`, `image`, `capture`, `config`, `clean`, `sleep`, `tools`, `learn` |
| **AI** | `agent` |
| **MCP** | `mcp` |

---

## see - Capture & Analyze UI

Captures a screenshot and returns UI element map with Peekaboo IDs.

```shell
peekaboo see --app <App> --json           # Capture app UI as JSON
peekaboo see --app <App> --annotate       # Annotated screenshot with markers
peekaboo see --mode screen                # Capture full screen
peekaboo see --mode screen --screen-index 0 --analyze "Describe the screen"
```

**Key options:**
- `--app <App>`: Target app name, bundle ID, or `frontmost`
- `--json` / `-j`: JSON output (includes `snapshot_id`, `ui_elements`)
- `--annotate`: Save annotated screenshot with element markers
- `--path <path>`: Custom output path for screenshot
- `--analyze <prompt>`: Analyze content with AI
- `--mode <mode>`: `screen`, `window`, `frontmost`
- `--window-title <title>`: Target specific window

**JSON output structure:**
```json
{
  "data": {
    "snapshot_id": "UUID",
    "application_name": "AppName",
    "element_count": 100,
    "interactable_count": 30,
    "screenshot_raw": "/path/to/screenshot.png",
    "ui_elements": [
      {
        "id": "elem_12",
        "role": "button",
        "label": "ボタン",
        "description": "Submit",
        "value": "",
        "is_actionable": true
      }
    ]
  }
}
```

---

## click - Click UI Elements

```shell
peekaboo click --on "elem_12" --snapshot "SNAP_ID"      # Click by element ID
peekaboo click --on "elem_12" --snapshot "SNAP_ID" --double  # Double-click
peekaboo click --on "elem_12" --snapshot "SNAP_ID" --right   # Right-click
peekaboo click --coords 100,200 --app <App>              # Click at coordinates
peekaboo click "Submit" --app <App>                      # Click by text query
```

**Key options:**
- `--on <elem_ID>`: Element ID from `see` command
- `--snapshot <ID>`: Snapshot ID (uses latest if omitted)
- `--coords <x,y>`: Click at screen coordinates
- `--double`: Double-click
- `--right`: Right-click
- `--wait-for <ms>`: Wait for element to appear
- `--app <App>`: Target application

---

## type - Type Text

Types text into the currently focused element. **Text is a positional argument**, not a flag.

```shell
peekaboo type "Hello World" --app <App>          # Type text
peekaboo type "user@example.com" --app <App>     # Type email
peekaboo type "text" --return                     # Type and press Enter
peekaboo type "text" --clear                      # Clear field first, then type
peekaboo type "text" --delay 0                    # Type at maximum speed
peekaboo type "text" --wpm 150                    # Type at 150 WPM
```

**Key options:**
- `[text]`: Positional argument - the text to type
- `--return`: Press Return after typing
- `--clear`: Clear field before typing (Cmd+A, Delete)
- `--delay <ms>`: Delay between keystrokes
- `--wpm <num>`: Approximate words per minute (80-220)
- `--profile <type>`: `human` (default) or `linear`
- `--tab <N>`: Press Tab N times

**Escape sequences:** `\n` (newline), `\t` (tab), `\b` (backspace), `\e` (escape), `\\` (literal backslash)

---

## press - Press Keys

Sends individual key presses or sequences.

```shell
peekaboo press return                  # Press Enter
peekaboo press tab --count 3           # Press Tab 3 times
peekaboo press escape                  # Press Escape
peekaboo press delete                  # Press Backspace
peekaboo press up down left right      # Arrow key sequence
peekaboo press f1                      # Function key
```

**Available keys:**
- Navigation: `up`, `down`, `left`, `right`, `home`, `end`, `pageup`, `pagedown`
- Editing: `delete` (backspace), `forward_delete`, `clear`
- Control: `return`, `enter`, `tab`, `escape`, `space`
- Function: `f1`-`f12`

**Key options:**
- `--count <N>`: Repeat count
- `--delay <ms>`: Delay between key presses (default: 100ms)
- `--hold <ms>`: Hold duration per key (default: 50ms)

---

## hotkey - Keyboard Shortcuts

Simulates keyboard shortcuts (multiple keys simultaneously).

```shell
peekaboo hotkey "cmd,c"                  # Copy
peekaboo hotkey "cmd,v"                  # Paste
peekaboo hotkey "cmd,a"                  # Select All
peekaboo hotkey "cmd,shift,t"            # Reopen tab
peekaboo hotkey "cmd space"              # Spotlight
```

**Key names:**
- Modifiers: `cmd`, `shift`, `alt`/`option`, `ctrl`, `fn`
- Letters: `a`-`z`
- Numbers: `0`-`9`
- Special: `space`, `return`, `tab`, `escape`, `delete`, `arrow_up`, `arrow_down`, `arrow_left`, `arrow_right`
- Function: `f1`-`f12`

**Options:**
- `--keys <keys>`: Keys (comma or space-separated)
- `--hold-duration <ms>`: Delay between press and release

---

## scroll - Scroll

```shell
peekaboo scroll --direction down --amount 5 --app <App>
peekaboo scroll --direction up --amount 10 --app <App>
peekaboo scroll --direction right --amount 3 --smooth
peekaboo scroll --direction down --amount 5 --on elem_42  # Scroll on specific element
```

**Options:**
- `--direction <dir>`: `up`, `down`, `left`, `right`
- `--amount <N>`: Number of scroll ticks
- `--smooth`: Use smooth scrolling
- `--on <elem_ID>`: Scroll on a specific element
- `--delay <ms>`: Delay between ticks

---

## paste - Clipboard Paste

Sets clipboard, pastes (Cmd+V), then restores previous clipboard content.

```shell
peekaboo paste "Hello" --app TextEdit
peekaboo paste --file-path /tmp/image.png --app Notes
peekaboo paste --data-base64 "$BASE64" --uti public.rtf --app TextEdit
```

**Options:**
- `[text]` or `--text <text>`: Text to paste
- `--file-path <path>`: File to paste
- `--image-path <path>`: Image to paste (alias for `--file-path`)
- `--data-base64 <data>`: Base64 data
- `--uti <uti>`: UTI for binary data

---

## app - Application Control

```shell
peekaboo app launch "AppName"                      # Launch
peekaboo app launch "AppName" --wait-until-ready    # Launch and wait
peekaboo app launch "Safari" --open https://example.com  # Launch with URL
peekaboo app quit --app <App>                       # Quit
peekaboo app quit --all --except "Finder,Terminal"  # Quit all except listed
peekaboo app relaunch "AppName"                     # Restart
peekaboo app switch --to <App>                      # Switch to app
peekaboo app hide --app <App>                       # Hide
peekaboo app unhide --app <App>                     # Show
peekaboo app list --json                            # List running apps
```

---

## window - Window Management

```shell
peekaboo window list --app <App> --json             # List windows
peekaboo window focus --app <App>                    # Bring to front
peekaboo window focus --app <App> --window-title "Login"  # Focus specific window
peekaboo window move --app <App> --x 100 --y 100    # Move
peekaboo window resize --app <App> --width 1200 --height 800  # Resize
peekaboo window set-bounds --app <App> --x 50 --y 50 --width 1024 --height 768
peekaboo window minimize --app <App>                 # Minimize
peekaboo window maximize --app <App>                 # Maximize/fullscreen
peekaboo window close --app <App>                    # Close
peekaboo window close --window-id 12345              # Close by window ID
```

---

## menu - Menu Bar Interaction

```shell
peekaboo menu list --app <App>                              # List all menu items
peekaboo menu click --app <App> --item "New Window"         # Click menu item
peekaboo menu click --app <App> --path "Format > Font > Show Fonts"  # Nested menu
peekaboo menu click-extra --title "WiFi"                    # System menu extras
peekaboo menu list-all                                       # All menu bar items
```

---

## permissions - Check Permissions

```shell
peekaboo permissions              # Check current status (default)
peekaboo permissions status       # Same as above
peekaboo permissions grant        # Show grant instructions
```

---

## Common Target Options

These options work with most interaction commands:

| Option | Description |
|---|---|
| `--app <App>` | App name, bundle ID, or `PID:12345` |
| `--pid <pid>` | Target by process ID |
| `--window-id <id>` | Target by CoreGraphics window ID |
| `--window-title <title>` | Target by window title (partial match) |
| `--window-index <N>` | Target by index (0-based, frontmost=0) |
| `--json` / `-j` | Machine-readable JSON output |
| `--verbose` / `-v` | Enable verbose logging |
| `--no-remote` | Force local execution |
