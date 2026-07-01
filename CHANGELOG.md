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
- **Self-contained, not a `kiro-wayland-dotfiles` consumer** — that shared package's
  `depends=(mako hyprlock hypridle)` would force-install unused hyprlock/hypridle. Ships its own
  `mako/config` + `waybar/colors.css`+`style.css` copies instead (same Tokyo Night palette).
- Not yet verified on a real niri boot: waybar window-title module (dropped for now, no confirmed
  niri equivalent), gtklock visual rendering, swayidle timeout behavior under niri's
  idle-notify protocol implementation. Same "verify on real boot" caveat kiro-wayfire carries.

### Files Modified
- `etc/skel/.config/niri/config.kdl` + `cfg/*.kdl` + `keybindings.txt` + `bg/kiro.jpg` +
  `scripts/import-gsettings.sh`
- `etc/skel/.config/waybar/config-ohmyniri.jsonc`, `colors.css`, `style.css`
- `etc/skel/.config/mako/config`
- `etc/skel/.config/gtklock/config.ini`, `style.css`
- `etc/dconf/db/local.d/00-kiro.conf`, `etc/dconf/profile/user`
- `README.md`, `CLAUDE.md`, `up.sh`, `setup.sh`
