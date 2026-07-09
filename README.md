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

- `etc/skel/.config/kiro-ohmyniri/` — the niri config, modular: `config.kdl` `include`s `cfg/*.kdl`
  (`keybinds`, `input`, `layout`, `rules`, `misc`, `animation`, `autostart`, `display`), plus a
  `keybindings.txt` cheat sheet, the wallpaper, and `scripts/import-gsettings.sh`.
- `etc/skel/.config/waybar/config-ohmyniri.jsonc` — native `niri/workspaces` module. The
  `colors.css`/`style.css`/`mako/config` and the system-wide dark GTK + Kiro cursor dconf defaults
  are **not** shipped here — they come from `kiro-wayland-dotfiles`, shared with the other waybar
  editions.
- `etc/skel/.config/gtklock/` — lock screen config + style, Tokyo Night.

## How to install

```sh
sudo pacman -S kiro-ohmyniri
```

`kiro-ohmyniri` depends on `niri` + `waybar` + `swaybg` + `rofi` + `gtklock` + `swayidle` +
`kiro-wayland-dotfiles` (mako + waybar colours, pulling in `hyprlock`/`hypridle` unused) plus the
usual Wayland helpers — all from Arch `extra`. On a fresh login niri starts the shell directly
(no Quickshell). Press **Super + Ctrl + S** for the searchable keybindings cheat sheet, or
**Super + Shift + /** for niri's built-in hotkey overlay.

A pristine copy of the config is kept at `/usr/share/kiro/kiro-ohmyniri/` so it can be restored.

**Co-installable with `kiro-niri`** — each edition ships its own namespaced config folder
(`~/.config/kiro-ohmyniri/` vs `~/.config/kiro-niri-noctalia/`) and its own session that points
niri there via `NIRI_CONFIG`, so there are no shared paths. The old reciprocal
`conflicts=('kiro-niri')` was dropped (2026.07.03); install both and pick one per-login.
