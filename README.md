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
| Terminal      | st       | `st_config.h.j2`, `files/st-0.8.2-neovim.patch` |
| Shell         | bash, fzf | `bashrc.j2`, `.bash_aliases.j2`             |
| Editor        | Neovim, LazyVim | `nvim/`                               |
| Screenshots   | flameshot | `roles/base/tasks/main.yml` ("Configure flameshot") |
| Clipboard     | CopyQ    | `roles/base/tasks/main.yml` ("Configure CopyQ") |
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
| `picom_backend`              | `glx`              | Use `egl` or `xrender` if the screen does not update |
| `picom_animations`           | `true`             | Animations when windows open and close        |
| `bash_history_size`          | `100000`           | Commands in the history that Ctrl+R searches  |
| `default_editor`             | `/usr/local/bin/nvim` | Editor for git, crontab, sudoedit. Use `/bin/nano` for nano |
| `neovim_version`             | `v0.12.6`          | Neovim release from GitHub                    |
| `lazyvim_extras`             | Python, TypeScript, Go, ... | LazyVim extras (languages)           |
| `screenshot_path`            | `~/Pictures/Screenshots` | Folder for screenshots                  |
| `copyq_max_items`            | `500`              | Items in the clipboard history                |

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
| `Super + Shift + X`      | Power menu                                |
| `Super + Shift + S`      | Apply the monitor layout                  |
| `Super + Shift + R`      | Restart xmonad                            |
| `Print`                  | Screenshot: select an area, draw, then copy (`Enter`) or save (`Ctrl + S`) |
| `Super + Print`          | The same, after 3 seconds (to capture a menu) |
| `Shift + Print`          | Screenshot of this monitor                |
| `Ctrl + Print`           | Screenshot of all monitors                |
| `Super + V`              | Clipboard history                         |

You can also click the bar: workspaces, the layout name, the Debian logo (launcher),
Wi-Fi and Bluetooth (click: menu, right click: on/off), the volume (click: mixer,
scroll: volume, middle click: mute), the keyboard layout and the power icon.

Wi-Fi and Bluetooth are only in the bar when the device has the hardware. The
playbook then installs NetworkManager (`network-manager`) and BlueZ (`bluez`).
The Wi-Fi menu lists the networks and asks for the password of a new network.
The Bluetooth menu connects paired devices, and scans for new devices to pair.
When a keyboard needs a passkey to pair, a notification shows the passkey to type on it.

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

The window title of st is `user@host: directory`, so each tab of the tabs
layout shows its directory.

`~/.config/bash/bashrc` also loads the developer tools that you install by hand:
nvm, pyenv (with pyenv-virtualenv), Pulumi, uv and Rust. Each tool loads only
when it is installed.

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

st 0.8.2 gets a small patch for Neovim 0.12 (`files/st-0.8.2-neovim.patch`).
Without it, st shows "â" in place of lines and icons.

### Screenshots

flameshot saves the screenshots in `~/Pictures/Screenshots`, with the date and
time in the file name. A copy to the clipboard also saves the file. The
`screenshot` and `xscreenshot` aliases use flameshot too.

### Clipboard

CopyQ keeps the clipboard history: text and images, also after the program
that copied them closes. Press `Super + V`, type to search, and press `Enter`
to paste the item. `Esc` closes the window.

### Monitors

Use `arandr` to set the monitor layout, then save it as
`~/.screenlayout/screen.sh`. xmonad runs this file when it starts.

# TODO:
- automate Nvidia dirver installation & configuration
- firefox tabs backup
- OBS config
- enable steam Nvidia drivers
