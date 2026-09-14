# HyprVim Release Notes

## [v2.1.0](https://github.com/uhs-robert/hyprvim/releases/tag/v2.1.0) - 2026-09-14

### New Features

- **Diagonal cursor movement**: chord binds (for example `H+K`) move the cursor omni-directionally across every speed tier, in addition to the cardinal `H`/`J`/`K`/`L` axes. Both key orderings are bound, since Hyprland chords are order-sensitive.
- **QuickClick submap**: `LEADER+;` gives one-shot label-jump clicking without entering the full Cursor submap. Cursor mode also gains reordered click mappings and right-click variants for `wl-kbptr`.
- **Submap groups sorted into their own section**: nested submaps now sort to the bottom of the which-key list by name instead of being interleaved with plain bindings.
- **Submap exit keys hidden from the list**: `ESC` and `BS` already appear in the HUD footer, so the per-submap exit rows are gone.
- **Configurable row density**: new `$row_padding_y` in `theme.conf` tunes the vertical padding on each key row.

### Bug Fixes

- **Temp files stay private**: clipboard reads, register previews and prompt input used `os.tmpname`, leaving mode-0644 files in `/tmp` holding whatever you copied or typed, under a name any local user could predict. They now live under `$XDG_RUNTIME_DIR/hyprvim/tmp` (0700).
- **Center layouts scale to the item count**: a six-item submap no longer renders as four near-empty columns; the column count follows how many rows actually fit the monitor.
- **Overflow stays on the configured edge**: a long list on a top-anchored HUD no longer flips to bottom-center.
- **Vertical layout kept on more monitors**: panel height was overestimated and rotated monitors reported their unrotated height, so the column fallback fired on screens where the list fit fine. Chrome and row height now come from the measured widget.
- **`BackSpace` labels normalized to `BS`**: non-exit bindings on that key rendered the raw Hyprland name.

### Internal

- Dead which-key panel-width plumbing removed, along with a file handle leaked on every submap change.

## [v2.0.1](https://github.com/uhs-robert/hyprvim/releases/tag/v2.0.1) - 2026-06-16

### Bug Fixes

- **Replace and open-editor no longer break later keystrokes**: `r`/`R` replace and `vim-open-editor` used `wtype`, which swapped Hyprland's active keymap and left subsequent native `send_shortcut` injections (the delete/change cut, paste) silently dropped until reset. Both now use Hyprland's native `send_shortcut` for all key emulation.
- **Updater skips redundant stable updates**: the auto-updater no longer re-fetches and checks out when HEAD is already at or past the latest release tag.

### Removed

- **`wtype` dependency**: no longer required; all keybind emulation now runs through native `send_shortcut`.
- **`input_method` config option**: replace always uses native injection with clipboard paste.

## [v2.0.0](https://github.com/uhs-robert/hyprvim/releases/tag/v2.0.0) - 2026-06-13

### Breaking Changes

- **Lua rewrite for Hyprland 0.55+**: HyprVim is now a modular Lua plugin for Hyprland's new Lua config format. Your config must be migrated to Lua. Legacy `.conf` users should stay on the [`legacy-conf`](https://github.com/uhs-robert/hyprvim/tree/legacy-conf) branch.
- **Install path moved to XDG data dir**: HyprVim now installs to `$XDG_DATA_HOME` instead of the config dir.
- **User theme/config moved to `$XDG_CONFIG_HOME`**: Custom themes and overrides now live under the XDG config directory.

### New Features

- **Auto-updater**: New update checker with `stable`, `nightly`, and `pinned` channels, plus handling for package-managed and detached nightly installs.
- **User-defined commands**: Define your own `:` commands via config. Adds 10+ built-in commands (opacity/dim via `set_prop`, resize, size, gaps, tab, move-pixel, and more) with string workspace selectors. In NORMAL mode, type `:help` to see them all.
- **Command completion cycling**: Repeated `Tab` cycles through completions in the command/prompt bar.
- **User keybind overrides**: New `keymaps` config lets you override submap binds.
- **Public API from `setup()`**: `setup()` now exposes a public API for programmatic use.
- **`*` register and live previews**: Adds the `*` selection register, live register-content previews in the which-key HUD, fallback register labels, numbered-register cycling on yank, and count multipliers for paste.
- **Clipboard persistence**: The system clipboard is now preserved across vim-mode sessions.
- **Configurable replace input**: Non-blocking replace prompts with a configurable input method.
- **Editor returns to submap**: `vim-open-editor` returns to the originating submap on exit.
- **Per-submap which-key timing**: Per-submap `delay_ms` and a global `vim_delay_ms`, with instant HUD display and a slide animation for the which-key layer.
- **New extras**: Tridactyl config and docs; Thunderbird `tbkeys` gains count and visual-mode support. The `wl-kbptr` extra has been removed.

### Improvements

- Bash scripts replaced by Lua modules throughout (window rules, terminal-class detection, theme apply, editor I/O, updater).
- Terminal-based prompt bar replaces the external menu tool; prompts and clipboard I/O migrated to async APIs supporting concurrent prompts.
- Declarative `Submap.define` API with a submap registry and aliases; submap transitions routed through a central Submap API.
- Custom terminal flags and user tables are deep-merged.
- Documentation moved from the GitHub wiki into the repo under `docs/guide` (allowing guides to be version controlled).

### Performance

- **In-process dispatch**: Logic runs natively in Hyprland's Lua runtime instead of spawning a shell (and often `jq`/`hyprctl`) per keystroke, cutting latency across motions, operators, and submap transitions.
- **Leaner which-key HUD**: Register items are built without `jq`, monitor geometry is passed straight to the renderer, redundant shell cleanup calls are removed, and hidden-HUD closes are skipped.
- **Fewer shell round-trips**: Find batches state-file access and the visual-line setup shell call is dropped.
