# swift-app-automator

[日本語](#日本語) | [English](#english)

---

## English

A [Claude Code Skill](https://docs.anthropic.com/en/docs/claude-code/skills) that enables AI agents to automate native macOS desktop applications using the Accessibility API via [Peekaboo](https://github.com/steipete/Peekaboo) CLI.

### What It Does

This skill teaches Claude Code how to **see, click, type, scroll, and control** any native macOS application — Calculator, ChatGPT, Finder, Notes, or any Swift/AppKit/Electron app.

#### Capabilities

- **Capture UI** — snapshot any app's interface and get structured element IDs
- **Click elements** — single, double, or right-click by element ID or coordinates
- **Type text** — type into any focused input field with human-like cadence
- **Keyboard shortcuts** — press keys, hotkeys, and key sequences
- **Scroll** — scroll in any direction with configurable amount
- **App control** — launch, quit, relaunch, switch between apps
- **Window management** — move, resize, minimize, maximize, close windows
- **Menu bar** — click menu items and navigate nested menus
- **Clipboard** — paste text, images, or files via clipboard

### Requirements

- **macOS 15+** (Sequoia or later)
- **Homebrew** installed
- **Accessibility** and **Screen Recording** permissions granted to your terminal

### Installation

#### 1. Install the Skill

```shell
npx skills add nogu66/swift-app-automator
```

Or manually copy the skill directory into your project's `.claude/skills/` folder.

#### 2. Install Peekaboo

```shell
brew tap steipete/tap && brew install peekaboo
```

#### 3. Grant Permissions

Open **System Settings > Privacy & Security** and enable:
- **Accessibility** — for your terminal app
- **Screen & System Audio Recording** — for your terminal app

Verify with:

```shell
peekaboo permissions
```

### Quick Start

Once installed, simply ask Claude Code to interact with any macOS app:

```
> Open Calculator and calculate 42 * 7

> Type "Hello" into the Notes app

> Take a screenshot of Safari and describe what's on the page

> Click the "New Document" menu item in TextEdit
```

### How It Works

The skill follows a **See → Interact → Verify** loop:

```
1. peekaboo see --app <App> --json     → Capture UI elements with IDs
2. peekaboo click --on <elem_ID> ...   → Interact with elements
3. peekaboo see --app <App> --json     → Verify the result
```

#### Example: Automating Calculator

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

#### Example: Typing into ChatGPT

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

### Command Reference

| Category | Commands |
|---|---|
| Capture | `see` |
| Interaction | `click`, `type`, `press`, `hotkey`, `scroll`, `paste`, `drag`, `move`, `swipe` |
| System | `app`, `window`, `menu`, `menubar`, `dock`, `clipboard`, `dialog`, `space` |

See [`references/peekaboo_command_reference.md`](references/peekaboo_command_reference.md) for the full command reference.

### Project Structure

```
swift-app-automator/
  SKILL.md                                  # Skill definition (loaded by Claude Code)
  references/
    peekaboo_command_reference.md            # Full Peekaboo v3 CLI reference
  README.md                                 # This file
  LICENSE                                   # MIT License
```

### Powered By

- [Peekaboo](https://github.com/steipete/Peekaboo) — macOS CLI for GUI automation via Accessibility API
- [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills) — extensible skill system for Claude Code

### License

MIT License. See [LICENSE](LICENSE) for details.

---

## 日本語

macOS のネイティブデスクトップアプリケーションを、Accessibility API 経由で自動操作するための [Claude Code スキル](https://docs.anthropic.com/en/docs/claude-code/skills)です。CLI ツール [Peekaboo](https://github.com/steipete/Peekaboo) を利用します。

### 概要

このスキルを導入すると、Claude Code が macOS 上のあらゆるネイティブアプリ（計算機、ChatGPT、Finder、メモ、Swift/AppKit/Electron アプリ等）を **見て、クリックして、入力して、スクロールして、操作** できるようになります。

#### できること

- **UI キャプチャ** — アプリの画面をスナップショットし、要素IDを取得
- **クリック** — 要素IDまたは座標でシングル / ダブル / 右クリック
- **テキスト入力** — フォーカスされた入力欄に人間らしいタイピング速度で入力
- **キーボードショートカット** — キー押下、ホットキー、キーシーケンス
- **スクロール** — 上下左右に任意の量をスクロール
- **アプリ制御** — 起動、終了、再起動、アプリ切り替え
- **ウィンドウ管理** — 移動、リサイズ、最小化、最大化、閉じる
- **メニューバー** — メニュー項目のクリック、ネストメニューの操作
- **クリップボード** — テキスト、画像、ファイルの貼り付け

### 動作環境

- **macOS 15+**（Sequoia 以降）
- **Homebrew** がインストール済み
- ターミナルに **アクセシビリティ** と **画面収録** の権限が付与されていること

### インストール

#### 1. スキルのインストール

```shell
npx skills add nogu66/swift-app-automator
```

または `.claude/skills/` フォルダにスキルディレクトリを手動でコピーしてください。

#### 2. Peekaboo のインストール

```shell
brew tap steipete/tap && brew install peekaboo
```

#### 3. 権限の付与

**システム設定 > プライバシーとセキュリティ** で以下を有効にしてください：
- **アクセシビリティ** — ターミナルアプリに対して
- **画面収録と音声** — ターミナルアプリに対して

確認コマンド：

```shell
peekaboo permissions
```

### クイックスタート

インストール後、Claude Code に macOS アプリの操作を自然言語で依頼するだけです：

```
> 計算機を開いて 42 × 7 を計算して

> メモアプリに「こんにちは」と入力して

> Safari のスクリーンショットを撮って、画面の内容を教えて

> TextEdit の「新規書類」メニューをクリックして
```

### 仕組み

**See → Interact → Verify** のループで動作します：

```
1. peekaboo see --app <App> --json     → UI要素をIDつきでキャプチャ
2. peekaboo click --on <elem_ID> ...   → 要素を操作
3. peekaboo see --app <App> --json     → 結果を確認
```

#### 使用例：計算機の自動操作

```shell
# 起動
open -a Calculator

# UI をキャプチャして snapshot_id と要素IDを取得
SNAP_ID=$(peekaboo see --app Calculator --json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# 1 + 2 = をクリック
peekaboo click --on "elem_10" --snapshot "$SNAP_ID"
peekaboo click --on "elem_14" --snapshot "$SNAP_ID"
peekaboo click --on "elem_18" --snapshot "$SNAP_ID"
peekaboo click --on "elem_22" --snapshot "$SNAP_ID"

# 結果を確認
peekaboo see --app Calculator --json
```

#### 使用例：ChatGPT にテキスト入力

```shell
# UI をキャプチャ
SNAP_ID=$(peekaboo see --app ChatGPT --json \
  | python3 -c "import json,sys; print(json.load(sys.stdin)['data']['snapshot_id'])")

# テキスト入力欄をクリック
peekaboo click --on "elem_142" --snapshot "$SNAP_ID"

# テキストを入力して送信
peekaboo type "Hello, world!" --app ChatGPT
peekaboo press return --app ChatGPT
```

### コマンドリファレンス

| カテゴリ | コマンド |
|---|---|
| キャプチャ | `see` |
| 操作 | `click`, `type`, `press`, `hotkey`, `scroll`, `paste`, `drag`, `move`, `swipe` |
| システム | `app`, `window`, `menu`, `menubar`, `dock`, `clipboard`, `dialog`, `space` |

詳細は [`references/peekaboo_command_reference.md`](references/peekaboo_command_reference.md) を参照してください。

### プロジェクト構成

```
swift-app-automator/
  SKILL.md                                  # スキル定義（Claude Code が読み込む）
  references/
    peekaboo_command_reference.md            # Peekaboo v3 全コマンドリファレンス
  README.md                                 # このファイル
  LICENSE                                   # MIT ライセンス
```

### 使用技術

- [Peekaboo](https://github.com/steipete/Peekaboo) — Accessibility API を利用した macOS GUI 自動化 CLI
- [Claude Code Skills](https://docs.anthropic.com/en/docs/claude-code/skills) — Claude Code の拡張スキルシステム

### ライセンス

MIT License. 詳細は [LICENSE](LICENSE) を参照してください。
