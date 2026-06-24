# tmux config

My tmux setup (rose-pine-moon, vi keys, mouse on), adapted from Kun Chen's config.
Lives at `~/.config/tmux/tmux.conf`. Paired with my [WezTerm config](https://github.com/richard12511/wezterm-lua).

**Prefix is `Ctrl+a`** (not the default `Ctrl+b`). Below, "`Ctrl+a` then `x`" means
press the prefix, release, then press `x`.

> ⚠️ **MacBook built-in keyboard gotcha:** the bottom-left corner key is `🌐/fn`,
> and **Control is the *next* key to the right**. On most external keyboards the
> corner *is* Control — so if a `Ctrl+a` shortcut "does nothing" on the laptop,
> you're probably hitting the Globe key. Either use the real Control key, or remap
> it: **System Settings → Keyboard → Keyboard Shortcuts… → Modifier Keys →
> set 🌐 Globe → ⌃ Control** (and set "Press 🌐 key to" → Do Nothing).

---

## Mental model

- **pane** = one rectangle in a split layout.
- **window** = a full screen of panes (shown as tabs in the status bar at the top, e.g. `1:editor 2:logs`). Only one window is visible at a time.
- **session** = a named collection of windows, living in a **background server**. This is why tmux survives closing/crashing the terminal — see [Session persistence](#session-persistence).

---

## Cheat sheet

### Panes — split, move, resize
| Action | Keys |
|---|---|
| Split horizontally (divider is horizontal) | `Ctrl+a` then `"` |
| Split vertically (divider is vertical) | `Ctrl+a` then `%` |
| Move between panes | `Ctrl+a` then `h`/`j`/`k`/`l` |
| **Resize** pane (5 cells, repeatable) | `Ctrl+a` then `Shift`+`H`/`J`/`K`/`L` |
| Resize pane (1 cell, repeatable) | `Ctrl+a` then `Ctrl`+arrow |
| Zoom pane fullscreen (toggle) | `Ctrl+a` then `z` |
| Cycle preset layouts (e.g. equalize) | `Ctrl+a` then `Space` |
| Kill the current pane | `Ctrl+a` then `x` |
| **Resize by mouse** | just drag the pane border (mouse is on) |

> "Repeatable" = after the first press you can tap `H`/`J`/`K`/`L` again within ~½ second without re-pressing the prefix.

### Windows (tabs)
| Action | Keys |
|---|---|
| New window (keeps current dir) | `Ctrl+a` then `c` |
| Next / previous window | `Ctrl+a` then `n` / `p` |
| Jump to window by number | `Ctrl+a` then `1`…`9` |
| Visual window picker | `Ctrl+a` then `w` |
| Rename the current window | `Ctrl+a` then `,` |
| Kill the current window | `Ctrl+a` then `&` |

> Note: `Ctrl+a l` is **not** "last window" here — `l` is rebound to "select pane to the right."

### Break a pane out / merge it back
| Action | Keys / command |
|---|---|
| Break the focused pane into its own window | `Ctrl+a` then `!`  (or run `tmux break-pane`) |
| Break it out *without* switching to it | `tmux break-pane -d` |
| Merge a window's pane back into window 1 | from that window: `tmux join-pane -t 1` |

### Labelling panes (track multiple agents)
A title bar sits on top of every pane. Labels use a pane-scoped `@agent` variable so
they **stick** even when the program (e.g. Claude Code) sets its own terminal title.

| Action | Keys |
|---|---|
| Label / relabel the focused pane | `Ctrl+a` then `Shift`+`T`, type the label, Enter |

Labels reset if the tmux **server** restarts (resurrect/continuum restores layout, not these ad-hoc labels). Re-apply with `Ctrl+a T`.

### Copy mode (vi keys, → macOS clipboard)
| Action | Keys |
|---|---|
| Enter copy mode (scroll back) | `Ctrl+a` then `y` |
| Start selection | `v` |
| Copy selection to clipboard & exit | `Enter` (pipes to `pbcopy`) |
| Cancel | `Escape` |
| Copy by **mouse** | drag to select — copies to the clipboard on release |

> Escape hatch: to bypass tmux's mouse capture and use WezTerm's *native* selection
> (straight to the macOS clipboard), hold **`Shift`** while dragging, then `Cmd+C`.

### Session persistence
tmux runs in a background server, so a session outlives the terminal window
(close it, crash it, sleep the laptop — the session is still there).

| Action | Command / keys |
|---|---|
| List sessions | `tmux ls` |
| Attach to a session | `tmux attach -t work`  (or `tmux a`) |
| Detach cleanly (leave it running) | `Ctrl+a` then `d` |

Plus `tmux-resurrect` + `tmux-continuum` are installed (auto-save every 15 min, auto-restore on start), so layouts survive a reboot too.

---

## Two monitors, non-mirrored

Two terminal windows attached to the **same** session will *mirror* each other.
To show **different windows of the same session** on two monitors independently, use a
**grouped session**:

1. Open a second terminal window (in WezTerm: `Cmd+N`) and drag it to the other monitor.
2. In it, create a grouped session sharing the same windows:
   ```sh
   tmux new-session -s side -t work
   ```
3. Now each window can sit on a different tmux window — e.g. `Ctrl+a 1` on one monitor,
   `Ctrl+a 2` on the other. Switching one no longer moves the other.

---

## Plugins (via TPM)

- `tmux-plugins/tpm` — plugin manager
- `rose-pine/tmux` — theme (moon variant)
- `tmux-plugins/tmux-resurrect` — save/restore sessions
- `tmux-plugins/tmux-continuum` — automatic save/restore

Install/update plugins: `Ctrl+a` then `Shift`+`I` (TPM install), `Ctrl+a` then `Shift`+`U` (update).

## Reloading after edits
```sh
tmux source-file ~/.config/tmux/tmux.conf
```
