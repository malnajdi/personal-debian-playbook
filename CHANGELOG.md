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
* VSCode uses the theme of the palette (Catppuccin Mocha or Tokyo Night) with the accent color of the desktop.
* The playbook manages the bash history and fzf: all the history is kept, Ctrl+R searches the history of all open terminals, and fzf uses the theme colors.
* The playbook loads the developer tools (nvm, pyenv, Pulumi, uv, Rust) in bash when they are installed, and removes their hand-added lines from `~/.bashrc`.
* Images open in nsxiv (with the other images of their folder) and PDFs open in zathura, with the theme colors and vim keys.
* The playbook installs uv, pyenv (with pyenv-virtualenv and Python 3.12) and nvm (with Node.js 24). Programs that start from the desktop also get Node.js, so Mason in Neovim works from the launcher.
* Neovim 0.12 with LazyVim. The color scheme follows the theme, and Neovim is the default editor.
* New windows open on the right (at the end of the stack), not as the main window on the left.
* flameshot for screenshots: `Print` keys, files in `~/Pictures/Screenshots`, theme colors.
* CopyQ clipboard history with text and images: `Super + V`.
* Power settings menu (in the power menu): keep awake (coffee icon in the bar), screen off time, lock when the screen turns off, and sleep when idle (not while sound plays). The screen locks before sleep, and the power button opens the power menu. The NVIDIA driver keeps the video memory during sleep.
* autorandr keeps the monitor layout with the refresh rates, and applies it at login, when a monitor connects or disconnects, and after sleep. picom syncs to the primary monitor, so it draws at its refresh rate.

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
* bash cut the history file to 2000 lines each time a terminal opened (`HISTFILESIZE` in `~/.bashrc`).
* fzf `Alt+C` used `fd`, but Debian calls it `fdfind`.
* st stopped to decode UTF-8 after a DCS string (st 0.8.2 bug), and did not answer the status report request. Neovim 0.12 showed "â" and the error E1568.
* The `screenshot` alias saved one file each day in the current directory, and a second screenshot on the same day replaced the first.
* rofi opens terminal applications in st.
* st shows `user@host: directory` as the window title (and in the tabs layout), not `st`.
* A long directory path left no space to type the command. The prompt now has two lines, shows the parent directories with their first letter, and shows the git branch, the Python virtual environment and the status of the last command.
