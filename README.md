# kiro-ohmyniri

The **batteries-included niri desktop edition** of Kiro — the niri edition with
[kiro-hyprland](https://github.com/kirodubes/kiro-hyprland)'s look and feel, sibling to
[kiro-niri](https://github.com/kirodubes/kiro-niri).

## What it is

A configuration package: the source-of-truth config tree for the Kiro ohmyniri edition. niri is a
scrollable-tiling Wayland compositor; the desktop shell here is the classic Kiro stack —
**waybar + mako + swaybg + rofi + gtklock**, static Tokyo Night theming — instead of
`kiro-niri`'s noctalia-shell. Same naming idea as `ohmychadwm` being the batteries-included
sibling of `kiro-chadwm`.

## What it ships

- `etc/skel/.config/niri/` — the niri config, modular: `config.kdl` `include`s `cfg/*.kdl`
  (`keybinds`, `input`, `layout`, `rules`, `misc`, `animation`, `autostart`, `display`), plus a
  `keybindings.txt` cheat sheet, the wallpaper, and `scripts/import-gsettings.sh`.
- `etc/skel/.config/waybar/` — `config-ohmyniri.jsonc` (native `niri/workspaces` module) +
  `colors.css`/`style.css`, Tokyo Night.
- `etc/skel/.config/mako/config` — notifications, Tokyo Night.
- `etc/skel/.config/gtklock/` — lock screen config + style, Tokyo Night.
- `etc/dconf/` — system-wide dark GTK + Kiro cursor defaults.

## How to install

```sh
sudo pacman -S kiro-ohmyniri
```

`kiro-ohmyniri` depends on `niri` + `waybar` + `mako` + `swaybg` + `rofi` + `gtklock` +
`swayidle` plus the usual Wayland helpers — all from Arch `extra`. On a fresh login niri starts
the shell directly (no Quickshell). Press **Super + Ctrl + S** for the searchable keybindings
cheat sheet, or **Super + Shift + /** for niri's built-in hotkey overlay.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-ohmyniri/` so it can be restored.

**Not co-installable with `kiro-niri`** (`conflicts=('kiro-niri')`, reciprocal) — both ship the
same `~/.config/niri/` paths, and niri itself always reads `~/.config/niri/config.kdl`, so pick
one niri edition.
