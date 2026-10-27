# Changelog

## Unreleased

### ⚠️ Breaking Changes
* The playbook targets Debian 13 (trixie). Some package names (for example `eza` and `picom`) are not in Debian 12.
* The default branch is `main` (was `master`).

### Features
* One color palette (Catppuccin Mocha or Tokyo Night) for xmonad, xmobar, picom, rofi, dunst, st, GTK and the login screen.
* New xmobar design: floating bar with icons, clickable workspaces and layout, CPU, memory, disk, network, volume, keyboard layout, date and a power menu.
* One xmobar on each monitor, the same bar on all monitors. Bars follow monitor changes.
* picom 12 configuration: rounded corners, shadows, blur, fading, dim of inactive windows and open/close animations.
* rofi launcher (`Super + P`), command runner, alias menu and power menu (`Super + Shift + X`) with the theme.
* dunst notifications with the theme.
* Dark GTK theme with Papirus icons, and a themed LightDM greeter that starts XMonad.
* JetBrainsMono Nerd Font is installed from the Nerd Fonts release.
* Wi-Fi and Bluetooth in the bar with rofi menus (connect, pair, on/off), when the device has the hardware.

### Fixes
* APT sources use the deb822 format (`/etc/apt/sources.list.d/debian.sources`).
* The i386 architecture is added with `dpkg --add-architecture`.
* Packages renamed or removed in Debian 13: `exa` to `eza`, `compton` to `picom`, `ibus-table-emoji` to `ibus-table-emoticon`, `pulseaudio` to PipeWire.
* Added the packages that a fresh install needs: X server, `xdotool`, `nano` and the libraries to build st.
* The Steam license is accepted before the install, so the install does not stop.
* Polkit uses a JavaScript rule (Debian 13 does not read `.pkla` files).
* sysctl values go to `/etc/sysctl.d` (Debian 13 does not read `/etc/sysctl.conf`).
* nano reads its configuration from `~/.config/nano/nanorc` with the syntax files of Debian.
* Handlers do not fail when no X session runs (fresh install).
* Paths use the `user` variable, not a fixed home directory.
* The bar shows the window title without xmobar tags from the title.
* The st font is 15 px (was 20 px), the same size as the rest of the desktop.
* VSCode settings are installed as `settings.json` (VSCode did not read `vscode.json`), and zen mode does not make the window full screen.
