# Changelog

## 2026.07.03

### What Changed
- **Own session entry + own config folder (`~/.config/kiro-ohmyniri/`).** kiro-niri and kiro-ohmyniri
  both boot the upstream `niri.desktop` and shared `~/.config/niri/`, so switching editions inherited
  the other's stale config. Now this edition ships **`kiro-ohmyniri.desktop`** ("Kiro OhMyNiri") → a
  **`kiro-ohmyniri-session`** wrapper that points niri at `~/.config/kiro-ohmyniri/config.kdl` via
  **`NIRI_CONFIG`**, and the whole niri config tree moved from `etc/skel/.config/niri/` to
  `etc/skel/.config/kiro-ohmyniri/` (relative `./cfg/*.kdl` includes come along untouched). The upstream
  plain "Niri" greeter entry is hidden with a `NoDisplay=true` pacman hook (same pattern as kiro-mango).

### Technical Details
- `kiro-ohmyniri-session`: sets `NIRI_CONFIG` (both `export` and `systemctl --user set-environment`), then
  `exec niri-session`. niri-session's own `systemctl --user import-environment` propagates it into
  `niri.service`.
- Hide hook + helper (`usr/bin/kiro-ohmyniri-hide-upstream-session`, `usr/share/libalpm/hooks/…`): `.install`
  applies on first install, hook re-applies after niri upgrades, `post_remove` strips it back out.
- Pairs with `archlinux-logout-gtk4` 26.07-07: `DESKTOP_SESSION=kiro-ohmyniri` now distinguishes the edition
  directly, so its logout kills the loose waybar/mako/swayidle/variety daemons then `niri msg action quit -s`.

### Files Modified
- `etc/skel/.config/niri/` → `etc/skel/.config/kiro-ohmyniri/` (moved)
- [etc/skel/.config/kiro-ohmyniri/config.kdl](etc/skel/.config/kiro-ohmyniri/config.kdl) (header path)
- [usr/bin/kiro-ohmyniri-session](usr/bin/kiro-ohmyniri-session) (new)
- [usr/share/wayland-sessions/kiro-ohmyniri.desktop](usr/share/wayland-sessions/kiro-ohmyniri.desktop) (new)
- [usr/bin/kiro-ohmyniri-hide-upstream-session](usr/bin/kiro-ohmyniri-hide-upstream-session) (new)
- [usr/share/libalpm/hooks/kiro-ohmyniri-hide-upstream-session.hook](usr/share/libalpm/hooks/kiro-ohmyniri-hide-upstream-session.hook) (new)
- [../KIROTUX-PKG-BUILD/kiro-ohmyniri/kiro-ohmyniri.install](../KIROTUX-PKG-BUILD/kiro-ohmyniri/kiro-ohmyniri.install)
- [../KIROTUX-PKG-BUILD/kiro-ohmyniri/PKGBUILD](../KIROTUX-PKG-BUILD/kiro-ohmyniri/PKGBUILD) (pkgrel 11 → 12)

## 2026.07.02

### What Changed
- **Moved `/etc/dconf/profile/user` + `/etc/dconf/db/local.d/00-kiro.conf` to
  `kiro-wayland-dotfiles`.** All 9 Kiro Wayland editions had shipped identical copies of these
  files, which conflicted in pacman whenever two editions were installed on the same machine.
  Settings unchanged — now owned once in the shared package (already a dependency here).

### Files Modified
- `etc/dconf/` (removed, moved to `kiro-wayland-dotfiles`)

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
