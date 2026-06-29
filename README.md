# ZGT — ZeDs Godot Terminal

A Godot 4 GDExtension (C++) that adds a built-in PTY terminal emulator to the editor's bottom panel. Runs a real shell (`$SHELL`) on a pseudoterminal with full VT/ANSI support, including truecolor, scrollback, selection, and clipboard. Works identically on **X11 and Wayland**.

![Godot 4.2+](https://img.shields.io/badge/Godot-4.2%2B-478cbf) ![Linux](https://img.shields.io/badge/Linux-only-fcc624)

## Install

### From a release

1. **Download** the release archive from the [Releases](https://github.com/zednaked/zgt-bin/releases) page
2. **Extract** into your Godot project root so the structure looks like:
   ```
   your-project/
   ├── addons/
   │   └── zgt/
   │       ├── plugin.cfg
   │       └── plugin.gd
   ├── bin/
   │   └── libzgt.linux.template_debug.x86_64.so
   └── zgt.gdextension
   ```
3. **Open** your project in the Godot editor
4. **Enable** the plugin: go to **Project → Project Settings → Plugins**, find **ZeDs Godot Terminal** and set it to **Enabled**
5. **Restart** the editor

The **ZGT** tab will appear in the bottom panel.

### Choosing the right binary

| File | When to use |
|---|---|
| `libzgt.linux.template_debug.x86_64.so` | Editor builds (`template_debug`) — use this in almost all cases |
| `libzgt.linux.template_release.x86_64.so` | Export templates (`template_release`) — only needed if you export your project |

The editor always uses the `template_debug` build. Drop both in `bin/` and Godot picks the right one automatically.

## Usage

- Click the **ZGT** tab in the bottom panel to open the terminal
- **`Ctrl+Shift+C`** to copy selection — **`Ctrl+Shift+V`** or **middle-click** to paste
- **Mouse wheel** to scroll through history (on the primary screen)
- Click **Restart** to kill and respawn the shell

Your default shell (`$SHELL`) runs automatically. To test a different one:

```bash
SHELL=/usr/bin/fish godot --editor
```

## Features

- **PTY shell**: `forkpty` + `execlp $SHELL -i` with `TERM=xterm-256color`
- **ANSI/VT parser**: cursor movement, erase (ED/EL), insert/delete lines/chars, scroll regions (DECSTBM), SGR (16/256/truecolor, bold, inverse, underline)
- **Alternate screen**: full support for `?1049`, `?47`, `?1047`
- **Scrollback**: 1000-line history buffer with mouse wheel scrolling
- **Selection & clipboard**: drag-select, `Ctrl+Shift+C/V`, middle-click paste (with bracketed-paste markers)
- **Fish compatibility**: replies to DSR cursor-position and Device Attributes queries
- **Truecolor**: 24-bit color support
- **Resize**: live grid reflow on panel resize

## Requirements

- **Godot 4.2+** (compatible with 4.3, 4.4, 4.5+)
- **Linux** (x86_64) — uses `forkpty` from `<util-linux>`
- **No X11/Wayland dependencies** — works on both, no `--display-driver` flag needed

## Build from source

You need C++17, `godot-cpp` 4.3, and SCons:

```bash
./setup.sh                              # clone godot-cpp
scons platform=linux                    # debug build
scons platform=linux target=template_release  # release build
```

Output goes to `bin/`.

## Troubleshooting

| Symptom | Likely cause |
|---|---|
| **ZGT tab doesn't appear** | Plugin not enabled, or `.so` missing from `bin/` |
| **Error "Failed to load GDExtension"** | `zgt.gdextension` missing or wrong path in project |
| **Shell doesn't start** | Missing `$SHELL` env var or shell not found |
| **Empty/black panel** | `forkpty` failed — check `dmesg` for ptY limits |
| **No color** | `TERM` incorrectly set; ensure `xterm-256color` |
