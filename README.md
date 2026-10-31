# personal-debian-playbook
Personal Debian Setup &amp; Configuration using Ansible

Target system: Debian 13 (trixie), amd64.

## Install on a fresh Debian 13

1. Install Debian 13. In the software selection, do not select a desktop
   environment. Select "standard system utilities".
2. Log in as your user. Install sudo, git and ansible, and add your user to the
   `sudo` group (the installer does this when you do not set a root password):
   ```sh
   su -c 'apt install -y sudo git ansible && /usr/sbin/usermod -aG sudo malnajdi'
   ```
   Log out and log in again.
3. Get the playbook:
   ```sh
   git clone -b main https://github.com/malnajdi/personal-debian-playbook.git
   cd personal-debian-playbook
   ```
4. Run the playbook. It asks for your sudo password:
   ```sh
   ansible-playbook -i inventory site.yml -K
   ```
5. Restart the computer. LightDM starts the XMonad session.

You can run the playbook again at any time. It only changes what is different.

## Desktop

| Part          | Program  | Configuration template                       |
|---------------|----------|----------------------------------------------|
| Window manager| xmonad   | `roles/base/templates/xmonad.hs.j2`          |
| Bars          | xmobar   | `xmobarrc.j2`                                |
| Compositor    | picom    | `picom.conf.j2`                              |
| Menus         | rofi     | `rofi.rasi.j2`, `powermenu.sh.j2`, `alias_dmenu.sh.j2` |
| Notifications | dunst    | `dunstrc.j2`                                 |
| Terminal      | st 0.9.3 | `st_config.h.j2`, `files/st/patches/`        |
| Shell         | bash, fzf | `bashrc.j2`, `.bash_aliases.j2`             |
| Editor        | Neovim, LazyVim | `nvim/`                               |
| Screenshots   | flameshot | `roles/base/tasks/main.yml` ("Configure flameshot") |
| Clipboard     | CopyQ    | `roles/base/tasks/main.yml` ("Configure CopyQ") |
| Images        | nsxiv    | `.Xresources.j2`, `files/nsxiv-browse`, `files/nsxiv-key-handler` |
| PDFs          | zathura  | `zathurarc.j2`                               |
| Monitors      | autorandr, arandr | `files/monitor-layout`, `.xsessionrc.j2` |
| Power         | xss-lock, i3lock | `power-settings.sh.j2`, `power.sh.j2`  |
| Login screen  | lightdm  | `lightdm-gtk-greeter.conf.j2`                |
| GTK apps      | Adwaita-dark, Papirus-Dark icons | `gtk-settings.ini.j2`, `.gtkrc-2.0.j2` |

xmonad starts one bar on each monitor. All monitors get the same bar. When you
connect or disconnect a monitor, the bars change too.

### Theme

One color palette controls all the programs above. The default is
`catppuccin_mocha`. To use Tokyo Night, run:

```sh
ansible-playbook -i inventory site.yml -K -e theme_name=tokyo_night
```

To add a palette, copy one entry in `theme_palettes` in
`roles/base/defaults/main.yml` and change the colors.

### Settings

All settings are in `roles/base/defaults/main.yml`. The most important are:

| Variable                     | Default            | Description                                   |
|------------------------------|--------------------|-----------------------------------------------|
| `theme_name`                 | `catppuccin_mocha` | Color palette                                 |
| `default_user_wallpaper_name`| `12`               | Wallpaper from `roles/base/files/wallpapers`  |
| `ui_font`                    | `JetBrainsMono Nerd Font` | Font for the terminal, menus and notifications |
| `xmonad_gap`                 | `6`                | Gap between windows, in pixels                |
| `xmonad_show_workspace_name` | `true`             | Show the workspace name when it changes       |
| `bar_height`                 | `34`               | Height of the bar                             |
| `bar_alpha`                  | `235`              | Bar transparency (0 to 255)                   |
| `st_alpha`                   | `95`               | Terminal background opacity in percent (the text stays opaque) |
| `picom_backend`              | `glx`              | Use `egl` or `xrender` if the screen does not update |
| `picom_animations`           | `true`             | Animations when windows open and close        |
| `bash_history_size`          | `100000`           | Commands in the history that Ctrl+R searches  |
| `default_editor`             | `/usr/local/bin/nvim` | Editor for git, crontab, sudoedit. Use `/bin/nano` for nano |
| `neovim_version`             | `v0.12.6`          | Neovim release from GitHub                    |
| `lazyvim_extras`             | Python, TypeScript, Go, ... | LazyVim extras (languages)           |
| `screenshot_path`            | `~/Pictures/Screenshots` | Folder for screenshots                  |
| `copyq_max_items`            | `500`              | Items in the clipboard history                |
| `power_screen_off_minutes`   | `10`               | Minutes without input before the screen turns off (`0`: never) |
| `power_lock_on_screen_off`   | `true`             | Lock the screen when it turns off             |
| `power_sleep_minutes`        | `0`                | Minutes without input before the computer sleeps (`0`: never) |
| `power_key`                  | `menu`             | Power button: `menu`, `suspend`, `poweroff` or `ignore` |
| `default_apps`               | nsxiv, zathura     | Default programs for file types               |
| `uv_version`                 | `0.9.28`           | uv release from GitHub                        |
| `pyenv_version`              | `v2.6.3`           | pyenv version (git tag)                       |
| `pyenv_python_versions`      | `3.12.11`          | Python versions that pyenv builds             |
| `nvm_version`                | `v0.40.3`          | nvm version (git tag)                         |
| `nvm_node_version`           | `v24.2.0`          | Default Node.js version                       |

### Keyboard shortcuts

`Super` is the Windows key.

| Keys                     | Action                                    |
|--------------------------|-------------------------------------------|
| `Super + Enter`          | Open a terminal (new windows open on the right) |
| `Super + P`              | Open the application launcher             |
| `Alt + Ctrl + Enter`     | Run a command                             |
| `Alt + Ctrl + P`         | Run a bash alias                          |
| `Super + Shift + T`      | Application grid                          |
| `Super + Shift + G`      | Go to a window                            |
| `Super + Shift + B`      | Bring a window                            |
| `Super + 1` to `7`       | Go to a workspace                         |
| `Super + Shift + 1` to `7` | Move the window to a workspace          |
| `Super + W`, `E`, `R`    | Go to monitor 1, 2, 3                     |
| `Super + Tab`            | Next layout (tall, monocle, floats, grid, tabs) |
| `Super + Ctrl + Tab`     | Reset the layout of the workspace         |
| `Super + Space`          | Full screen on and off                    |
| `Super + F`              | Floating layout on and off                |
| `Super + Shift + C`      | Close the window                          |
| `Alt + Shift + 1`        | Change the keyboard layout (US and Arabic)|
| `Super + Shift + L`      | Lock the screen                           |
| `Super + Shift + X`      | Power menu (also the power button)        |
| `Super + Shift + S`      | Apply the saved monitor layout again      |
| `Super + Shift + R`      | Restart xmonad                            |
| `Print`                  | Screenshot: select an area, draw, then copy (`Enter`) or save (`Ctrl + S`) |
| `Super + Print`          | The same, after 3 seconds (to capture a menu) |
| `Shift + Print`          | Screenshot of this monitor                |
| `Ctrl + Print`           | Screenshot of all monitors                |
| `Super + V`              | Clipboard history                         |

You can also click the bar: workspaces, the layout name, the Debian logo (launcher),
Wi-Fi and Bluetooth (click: menu, right click: on/off), the volume (click: mixer,
scroll: volume, middle click: mute), the keyboard layout, the coffee icon (keep
awake off) and the power icon.

Wi-Fi and Bluetooth are only in the bar when the device has the hardware. The
playbook then installs NetworkManager (`network-manager`) and BlueZ (`bluez`).
The Wi-Fi menu lists the networks and asks for the password of a new network.
The Bluetooth menu connects paired devices, and scans for new devices to pair.
When a keyboard needs a passkey to pair, a notification shows the passkey to type on it.

### Terminal (st)

The playbook builds st 0.9.3 with these patches from `roles/base/files/st/patches`.
The top of each patch file tells what it does and where it comes from.

- scrollback: 2000 lines of history.
- alpha: the background is transparent (`st_alpha`), the text is not.
- boxdraw: lines and boxes (Neovim borders, file trees) join with no gaps.
- ligatures: the font shows `=>`, `!=` and `->` as one symbol.
- undercurl: curly underlines for spelling and errors in Neovim.
- newterm and copyurl: the keys below.
- anysize: st fills its tile and centers the text.

| Keys                             | Action                                     |
|----------------------------------|--------------------------------------------|
| Mouse wheel                      | Scroll the history. In `less`, `man` and Neovim, the wheel scrolls the program. |
| `Shift + PageUp` / `PageDown`    | Scroll the history one page. A key press goes back to the bottom. |
| `Ctrl + Shift + Enter`           | New terminal in the same directory         |
| `Alt + L`                        | Copy the previous URL on the screen (press again for the URL before it) |
| `Alt + Shift + L`                | Copy the next URL                          |
| `Ctrl + Shift + C` / `V`         | Copy / paste with the clipboard            |
| `Ctrl + Shift + PageUp` / `PageDown` | Larger / smaller text (`Ctrl + Shift + Home`: normal size) |

### Shell

The playbook keeps all the bash history in `~/.bash_history`, with the date of
each command. Each command goes to the file when it ends, so a closed terminal
does not lose its commands.

| Keys          | Action                                                   |
|---------------|----------------------------------------------------------|
| `Ctrl + R`    | Search the history of all terminals (fzf)                |
| `Ctrl + T`    | Insert a file path                                       |
| `Alt + C`     | Go to a directory                                        |
| `** + Tab`    | Complete paths, ssh hosts and processes (`ssh **`, `kill **`) |

The prompt has two lines, so the command always gets the full width of the
window:

```
~/p/w/i/devops/ansible-playbook   main   .venv
❯
```

- The parent directories show only their first letter. The window title of st
  shows the full path (`user@host: directory`), so the bar and each tab of the
  tabs layout show it.
- The git branch shows in a git repository, and the Python virtual environment
  shows when one is active.
- `❯` is red when the last command failed.
- `user@host` shows only over SSH.

`~/.config/bash/bashrc` also loads the developer tools: nvm, pyenv (with
pyenv-virtualenv) and uv, which the playbook installs, and Pulumi and Rust, which
you install by hand. Each tool loads only when it is installed.

The playbook adds one block to the end of `~/.bashrc` that reads
`~/.config/bash/bashrc`. It removes the lines of fzf and of the developer tools
from `~/.bashrc`, because the block loads them now. Your other lines in
`~/.bashrc` stay.

### Neovim (LazyVim)

Debian 13 has Neovim 0.10. LazyVim needs Neovim 0.11.2 or newer and the
tree-sitter CLI 0.26 or newer, so the playbook installs them from GitHub to
`/opt`. Run `nvim`, or open "Neovim" in the launcher.

- The color scheme follows `theme_name`.
- Yanks go to the system clipboard, so they are also in the clipboard history.
- `:Lazy` shows the plugins, `:Mason` shows the language servers, `:LazyExtras`
  shows the extras. Add extras to `lazyvim_extras` to keep them.
- Edit the configuration in `roles/base/templates/nvim/`, then run the playbook.

### Screenshots

flameshot saves the screenshots in `~/Pictures/Screenshots`, with the date and
time in the file name. A copy to the clipboard also saves the file. The
`screenshot` and `xscreenshot` aliases use flameshot too.

### Clipboard

CopyQ keeps the clipboard history: text and images, also after the program
that copied them closes. Press `Super + V`, type to search, and press `Enter`
to paste the item. `Esc` closes the window.

### Images and PDFs

Images open in nsxiv and PDFs open in zathura, from Thunar, the browser or
`xdg-open`. Both use the colors of the theme and keys like vim. nsxiv opens an
image together with the other images of its folder.

| nsxiv keys            | Action                                    |
|-----------------------|-------------------------------------------|
| `n`, `p`              | Next and previous image (also `Space`, `Backspace`) |
| `Enter`               | Thumbnails of the folder, and back        |
| `+`, `-`, `=`         | Zoom in, zoom out, 100 %                  |
| `<`, `>`              | Rotate                                    |
| `m`                   | Mark the image (for the `Ctrl + X` keys)  |
| `Ctrl + X`, then `c`  | Copy the image to the clipboard           |
| `Ctrl + X`, then `y`  | Copy the path of the image                |
| `Ctrl + X`, then `d`  | Move the image to the trash               |
| `q`                   | Close                                     |

| zathura keys          | Action                                    |
|-----------------------|-------------------------------------------|
| `j`, `k`, `J`, `K`    | Scroll, next and previous page            |
| `gg`, `G`             | First and last page                       |
| `/`, `n`, `N`         | Search, next and previous result          |
| `+`, `-`, `a`, `s`    | Zoom in, zoom out, fit the page, fit the width |
| `Tab`                 | Table of contents                         |
| `Ctrl + R`            | Pages in the colors of the theme (dark)   |
| `q`                   | Close                                     |

Text that you select in zathura goes to the clipboard.

### Developer tools

The playbook installs these tools in your home directory:

- uv in `~/.local/bin`
- pyenv in `~/.pyenv`, with the pyenv-virtualenv plugin and the Python versions
  in `pyenv_python_versions`
- nvm in `~/.nvm`, with the Node.js version in `nvm_node_version` as the default

To update a tool, change its version in `roles/base/defaults/main.yml` and run
the playbook. The playbook puts back these versions after `uv self update` or
`pyenv update`. Programs that start from the desktop (for example Mason in
Neovim from the launcher) also get the default Node.js version.

### Monitors

autorandr keeps the monitor layout: the positions, resolutions, refresh rates
and the primary monitor. It keeps one layout for each set of monitors. It
applies the layout of the connected monitors when you log in, when you connect
or disconnect a monitor, and after sleep.

To change the layout:

1. Set the positions and resolutions with `arandr`, and click Apply. arandr
   cannot set the refresh rate, so set it with `nvidia-settings` (X Server
   Display Configuration, Refresh, then Apply; do not save the X
   configuration file), or with xrandr:
   ```sh
   xrandr --output DP-2 --mode 2560x1440 --rate 164.55
   ```
   `xrandr` shows the outputs and their refresh rates.
2. Save the layout with a name, for example `desk`:
   ```sh
   autorandr --save desk --force
   ```

`autorandr` shows the saved layouts. If no saved layout matches the connected
monitors, the session uses the layout that arandr saved in
`~/.screenlayout/screen.sh` (without refresh rates).

picom syncs to the primary monitor, so it draws at the refresh rate of that
monitor (for example 165 Hz, not 60 Hz).

### Power

Open the power menu (`Super + Shift + X`, the power button or the power icon),
then "Power settings":

| Item                            | Action                                        |
|---------------------------------|-----------------------------------------------|
| Keep awake                      | The screen does not turn off or lock, and the computer does not sleep. The bar shows a coffee icon. Click the icon to turn it off. |
| Screen off after                | Minutes without keyboard or mouse input before the monitors turn off |
| Lock when the screen turns off  | Lock the screen when the monitors turn off   |
| Sleep after                     | Minutes without input before the computer sleeps. It does not sleep while sound plays (a video or music). |
| Turn the screen off now         | The monitors turn off until you move the mouse or push a key |

Your choices apply immediately and stay after a restart (in
`~/.local/state/power-settings`). The defaults are the `power_*` settings.

The screen always locks before the computer sleeps. The power button opens the
power menu; a long press turns the computer off.

With the NVIDIA driver, the playbook sets the driver to keep the video memory
during sleep. Without it, the windows can be black or broken after sleep.
Restart the computer once after the playbook sets it.

# TODO:
- automate Nvidia dirver installation & configuration
- firefox tabs backup
- OBS config
- enable steam Nvidia drivers
