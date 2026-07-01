# Changelog

## 2026.07.01

### What Changed
- **Initial config package** for the Kiro ohmyniri edition — the niri edition with
  kiro-hyprland's look and feel, sibling to `kiro-niri` (which pairs niri with noctalia-shell).
  Establishes the `kiro-ohmy<wm>` naming convention for the KIROTUX line, modeled on
  `ohmychadwm`'s relationship to `kiro-chadwm`.
- Forked from `kiro-niri`'s compositor-level KDL config (window/column management, keybind
  grammar, app defaults) unchanged; swapped the desktop shell from noctalia-shell to
  **waybar + mako + swaybg + rofi + gtklock + swayidle**, static Tokyo Night theming.

### Technical Details
- `cfg/autostart.kdl` — replaced the single `qs -c noctalia-shell` spawn with individual spawns:
  swaybg (wallpaper), waybar, mako, a polkit agent, `import-gsettings.sh`, swayidle (idle → lock),
  and `variety` (wallpaper rotator, matching kiro-hyprland).
- `cfg/keybinds.kdl` — only the shell block changed: launcher binds → `rofi -show drun`/`run`,
  lock binds → `gtklock`, added kiro-hyprland's Variety wallpaper chords (`Alt+N/P/T/F/Up/Down/W`),
  media/brightness keys now call `wpctl`/`playerctl`/`brightnessctl` directly instead of noctalia
  IPC. Dropped the noctalia-only control-center/settings/wallpaper-selector chords
  (`Mod+S`/`Mod+Shift+S`/`Mod+Shift+W`) — no shell equivalent.
- `cfg/layout.kdl` — Tokyo Night focus-ring accent (`#7aa2f7`/`#414868`) instead of Catppuccin.
- `cfg/rules.kdl` — dropped the noctalia-wallpaper `layer-rule` (swaybg needs no special backdrop
  placement). `cfg/misc.kdl`'s `honor-xdg-activation-with-invalid-serial` kept (generically useful
  for mako/rofi activation, not noctalia-specific).
- **Lock/idle is gtklock + swayidle, not hyprlock/hypridle.** niri is Smithay-based, not wlroots
  — the community reference (niri-saatvik333's waybar rice) proved gtklock as the working
  niri-native choice; both packages are in Arch `extra` (no chaotic-aur/3PARTY packaging needed).
- **`kiro-wayland-dotfiles` consumer** for `mako/config` + `waybar/colors.css`/`style.css` (same
  Tokyo Night palette), matching every other waybar edition — reversed from an initial
  self-contained design that duplicated those same absolute paths (a real file-ownership conflict
  with any other `kiro-wayland-dotfiles` consumer installed alongside). Force-installs
  `hyprlock`/`hypridle` unused (that package's own `depends`), accepted to avoid the duplication.
  Ships only its own unique `waybar/config-ohmyniri.jsonc` + `gtklock/` config.
- Not yet verified on a real niri boot: waybar window-title module (dropped for now, no confirmed
  niri equivalent), gtklock visual rendering, swayidle timeout behavior under niri's
  idle-notify protocol implementation. Same "verify on real boot" caveat kiro-wayfire carries.

- **Declared `conflicts=('kiro-niri')` in the PKGBUILD (reciprocal — `kiro-niri`'s PKGBUILD now
  carries `conflicts=('kiro-ohmyniri')`).** Both packages ship identical absolute paths
  (`/etc/skel/.config/niri/**`, `etc/dconf/db/local.d/00-kiro.conf`, `etc/dconf/profile/user`),
  and niri always reads `~/.config/niri/config.kdl` regardless of which package installed it —
  so the two editions can never usefully coexist. Same pattern as
  `kiro-calamares-config-hyprland` vs `kiro-calamares-config(-next)`.

### Files Modified
- `etc/skel/.config/niri/config.kdl` + `cfg/*.kdl` + `keybindings.txt` + `bg/kiro.jpg` +
  `scripts/import-gsettings.sh`
- `etc/skel/.config/waybar/config-ohmyniri.jsonc`
- `etc/skel/.config/gtklock/config.ini`, `style.css`
- `etc/dconf/db/local.d/00-kiro.conf`, `etc/dconf/profile/user`
- `README.md`, `CLAUDE.md`, `up.sh`, `setup.sh`
- `KIROTUX-PKG-BUILD/kiro-ohmyniri/PKGBUILD` (`conflicts`, then `kiro-wayland-dotfiles` dep +
  dropped `mako`), `KIROTUX-PKG-BUILD/kiro-niri/PKGBUILD` (reciprocal `conflicts`)
- Removed: `etc/skel/.config/mako/config`, `etc/skel/.config/waybar/colors.css`, `style.css`
  (now sourced from `kiro-wayland-dotfiles`)
