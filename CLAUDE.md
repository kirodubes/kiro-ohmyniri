# kiro-ohmyniri — Claude project instructions

## Overview
Config package for the **Kiro ohmyniri edition** — the niri member of the KIROTUX Wayland line
that reuses [kiro-hyprland](../kiro-hyprland/CLAUDE.md)'s shell/look instead of noctalia-shell.
Sibling to [kiro-niri](../kiro-niri/CLAUDE.md), which is the noctalia-shell edition of the same
compositor. Public, open-core, shipped via `nemesis_repo`.

**Naming convention:** `kiro-ohmy<wm>` = the batteries-included / kiro-hyprland-look sibling of a
`kiro-<wm>` edition — same idea as `ohmychadwm` being the fuller sibling of `kiro-chadwm`. This is
the first WM to get the pattern; the ADR is in
`Kiro-HQ/Kirotux/KIROTUX-HYPRLAND-DECISION-REGISTER.md`.

## Edition spec (the WM-variable matrix)
- **Compositor:** niri (scrollable-tiling, Smithay-based — *not* wlroots). Compositor-level config
  (window/column management, KDL structure) is identical to `kiro-niri`.
- **Config language:** KDL. `etc/skel/.config/kiro-ohmyniri/config.kdl` `include`s `cfg/*.kdl`.
- **Desktop shell:** **waybar + mako + swaybg + rofi + gtklock** — kiro-hyprland's stack, ported.
  waybar uses the native `niri/workspaces` module (confirmed via the community reference
  `niri-saatvik333-waybar-rice`) — no custom IPC workspace hack needed, unlike wayfire.
- **Lock/idle: gtklock + swayidle**, NOT hyprlock/hypridle. niri is Smithay-based, not wlroots, so
  the hyprlock/hypridle combo the wlroots editions share is unverified here; gtklock is the
  niri-community-proven choice. Both packages are in Arch `extra`.
- **Autostart:** individual `spawn-*-at-startup` lines in `cfg/autostart.kdl` (swaybg, waybar,
  mako, polkit agent, `import-gsettings.sh`, swayidle, `variety` wallpaper rotator,
  `xdg-user-dirs-update`, archiso-gated Calamares line). niri does **not** process
  `/etc/xdg/autostart`.
- **Theming:** **static Tokyo Night**, matching kiro-hyprland exactly — not pywal (the
  wayfire/sway/river/labwc choice) and not noctalia/matugen (`kiro-niri`).
- **`kiro-wayland-dotfiles` consumer** (reversed 2026-07-01 — see ADR-014 addendum): depends on it
  for `mako/config` + `waybar/colors.css`/`style.css` (same Tokyo Night palette), same as every
  other waybar edition. Ships only its own `waybar/config-ohmyniri.jsonc` (unique path). This
  force-installs `hyprlock`+`hypridle` unused (kiro-wayland-dotfiles's own `depends`) since this
  edition locks/idles with gtklock/swayidle instead — accepted tradeoff for not duplicating the
  shared mako/waybar files (and the file-ownership conflict duplicating them would cause with any
  other kiro-wayland-dotfiles consumer installed alongside).
- **Dependencies:** everything from Arch `extra` — nothing packaged by Kiro (`gtklock`,
  `swayidle` both confirmed in `extra`, no chaotic-aur/3PARTY needed).
- **Co-installable with `kiro-niri` (noctalia edition) — `conflicts=` dropped 2026.07.03.**
  Each edition now ships a **namespaced** config folder (`etc/skel/.config/kiro-ohmyniri/` vs
  `kiro-niri-noctalia/`) and its own session wrapper that points niri at it via `NIRI_CONFIG`, so
  they no longer share `~/.config/niri/` paths. dconf defaults come from `kiro-wayland-dotfiles`
  (single owner), not from either edition. Both install together and are picked per-login — like
  the other KIROTUX editions, not the drop-in-replacement pattern of
  `kiro-calamares-config-wayland`.

## Keybindings
- niri-native window/column/workspace management **kept verbatim from kiro-niri** (column model:
  `focus-column-*`, `move-column-*`, consume/expel).
- Shell chords changed: launcher → `rofi -show drun`/`run`, lock → `gtklock`
  (`Mod+Alt+L`/`Ctrl+Alt+R`), media/brightness → direct `wpctl`/`playerctl`/`brightnessctl` calls.
  Added kiro-hyprland's Variety wallpaper chords (`Alt+N/P/T/F/Up/Down/W`). Dropped the
  noctalia-only control-center/settings/wallpaper-selector chords — no shell equivalent.
- `keybindings.txt` mirrors `cfg/keybinds.kdl` — keep them in lockstep; a duplicate-chord scan
  must pass. (`kiro-keybindings` / `/kiro-create-keybindings` need **niri** in their WM-detection
  table already — same gap `kiro-niri` hit; ohmyniri shares the fix.)
- Mod = Super. Belgian `be,us` layout.

## Patterns / gotchas
- niri `spawn` is **argv, no shell**; use `spawn-sh` for anything with pipes/`&&`/globs or `~`
  tilde expansion (swaybg's wallpaper path, the swayidle one-liner, the archiso-gated line).
- `debug { honor-xdg-activation-with-invalid-serial }` kept — useful for mako/rofi activation
  clicks generally, not noctalia-specific (the original comment was wrong).
- **Not yet verified on a real niri boot:** a waybar window-title module (dropped — no confirmed
  niri equivalent, unlike `hyprland/window`), gtklock's visual rendering, and swayidle's timeout
  behavior under niri's idle-notify protocol implementation. Verify before/at the first ISO test
  (same caveat kiro-wayfire and kiro-niri both carried at their own first boots).
- `niri msg action power-off-monitors` / `power-on-monitors` used in the swayidle DPMS hooks —
  confirm these IPC actions exist on the niri version Kiro ships.

## Build / delivery
- Source-of-truth for the config; delivered as the `kiro-ohmyniri` package via
  `../KIROTUX-PKG-BUILD/kiro-ohmyniri/build.sh` (public recipe → `~/EDU/nemesis_repo/`). After
  editing here: rebuild the package (recipe `build.sh`), then the ISO to test a fresh install.
- See [../CLAUDE.md](../CLAUDE.md) for the full KIROTUX delivery architecture.
