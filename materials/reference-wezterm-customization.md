# WezTerm カスタマイズ参考記事

出典: https://zenn.dev/mozumasu/articles/mozumasu-wezterm-customization

## 概要

モテるターミナルにカスタマイズ（WezTerm）

## WezTermの特徴

- Rust製でとにかく早い
- 軽量
- カスタマイズ性高め
- 設定ファイルはNeovimと同じLua言語
- tmuxのような画面分割ができる
- 豊富なドキュメントがある
- コピーモードが使いやすい（Leader + [ でVimキーバインド操作）

## インストール

```bash
# 通常版
brew install --cask wezterm

# nightly版（一部機能はnightly限定）
brew install --cask wezterm@nightly
```

## 設定ファイルの場所

- シンプル: `~/.wezterm.lua`
- 分割管理: `~/.config/wezterm/wezterm.lua` + `~/.config/wezterm/keybinds.lua`

## 基本設定

```lua
local wezterm = require 'wezterm'
local config = wezterm.config_builder()

config.automatically_reload_config = true
config.font_size = 12.0
config.use_ime = true  -- 日本語入力

return config
```

## 見た目の設定

### 背景透過 & ぼかし

```lua
config.window_background_opacity = 0.85
config.macos_window_background_blur = 20
```

### タイトルバー削除

```lua
config.window_decorations = "RESIZE"
```

### タブ設定

```lua
config.hide_tab_bar_if_only_one_tab = true
config.show_new_tab_button_in_tab_bar = false
config.show_close_tab_button_in_tabs = false  -- nightly限定

config.window_frame = {
  inactive_titlebar_bg = "none",
  active_titlebar_bg = "none",
}

config.window_background_gradient = {
  colors = { "#000000" },
}

config.colors = {
  tab_bar = {
    inactive_tab_edge = "none",
  },
}
```

### タブの形状とアクティブ色

```lua
local SOLID_LEFT_ARROW = wezterm.nerdfonts.ple_lower_right_triangle
local SOLID_RIGHT_ARROW = wezterm.nerdfonts.ple_upper_left_triangle

wezterm.on("format-tab-title", function(tab, tabs, panes, config, hover, max_width)
  local background = "#5c6d74"
  local foreground = "#FFFFFF"
  local edge_background = "none"

  if tab.is_active then
    background = "#ae8b2d"
    foreground = "#FFFFFF"
  end

  local edge_foreground = background
  local title = "   " .. wezterm.truncate_right(tab.active_pane.title, max_width - 1) .. "   "

  return {
    { Background = { Color = edge_background } },
    { Foreground = { Color = edge_foreground } },
    { Text = SOLID_LEFT_ARROW },
    { Background = { Color = background } },
    { Foreground = { Color = foreground } },
    { Text = title },
    { Background = { Color = edge_background } },
    { Foreground = { Color = edge_foreground } },
    { Text = SOLID_RIGHT_ARROW },
  }
end)
```

## キーバインド設定

### 現在のキーバインドをエクスポート

```bash
wezterm show-keys --lua > keybinds.lua
```

### Leaderキー設定

```lua
config.leader = { key = "q", mods = "CTRL", timeout_milliseconds = 2000 }
```

### keybinds.lua を読み込む

```lua
config.keys = require("keybinds").keys
config.key_tables = require("keybinds").key_tables
config.disable_default_key_bindings = true  -- デフォルト無効化する場合
```

## 公式ドキュメント

- https://wezfurlong.org/wezterm/
- GitHub Discussions: https://github.com/wez/wezterm/discussions/628
