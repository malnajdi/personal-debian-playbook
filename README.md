# personal-debian-playbook

This Ansible playbook installs and configures a personal Debian desktop.

Target system: Debian 13 (trixie), amd64.

## Install on a new Debian 13 system

1. Install Debian 13. In the software selection, do not select a desktop
   environment. Select "standard system utilities".
2. Log in as your user.
3. Install sudo, git and ansible, and add your user to the `sudo` group:
   ```sh
   su -c 'apt install -y sudo git ansible && /usr/sbin/usermod -aG sudo malnajdi'
   ```
   If you did not set a root password, the installer installed sudo and added
   your user to the `sudo` group. In this case, run
   `sudo apt install -y git ansible`.
4. Log out and log in again.
5. Download the playbook:
   ```sh
   git clone -b main https://github.com/malnajdi/personal-debian-playbook.git
   cd personal-debian-playbook
   ```
6. Run the playbook. When the playbook asks for your sudo password, type it:
   ```sh
   ansible-playbook -i inventory site.yml -K
   ```
7. Restart the computer. LightDM starts the XMonad session.

You can run the playbook again at any time. The playbook changes only the
items that are different.

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
| Login screen  | lightdm  | `lightdm-gtk-greeter.conf.j2`                |
| GTK apps      | Adwaita-dark, Papirus-Dark icons | `gtk-settings.ini.j2`, `.gtkrc-2.0.j2` |

xmonad starts one bar on each monitor. All the monitors show the same bar.
When you connect or disconnect a monitor, xmonad changes the bars.

### Theme

One color palette sets the colors of all the programs above. The default
palette is `catppuccin_mocha`. To use Tokyo Night, run this command:

```sh
ansible-playbook -i inventory site.yml -K -e theme_name=tokyo_night
```

To add a palette, copy one entry in `theme_palettes` in
`roles/base/defaults/main.yml`. Then change the colors.

### Settings

All the settings are in `roles/base/defaults/main.yml`. This table shows the
most important settings:

| Variable                     | Default            | Description                                   |
|------------------------------|--------------------|-----------------------------------------------|
| `theme_name`                 | `catppuccin_mocha` | Color palette                                 |
| `default_user_wallpaper_name`| `12`               | Wallpaper from `roles/base/files/wallpapers`  |
| `ui_font`                    | `JetBrainsMono Nerd Font` | Font of the terminal, the menus and the notifications |
| `xmonad_gap`                 | `6`                | Gap between the windows, in pixels            |
| `xmonad_show_workspace_name` | `true`             | Show the workspace name when you go to a different workspace |
| `bar_height`                 | `34`               | Height of the bar, in pixels                  |
| `bar_alpha`                  | `235`              | Opacity of the bar (0: transparent, 255: opaque) |
| `st_alpha`                   | `95`               | Opacity of the terminal background, in percent. The text stays opaque. |
| `picom_backend`              | `glx`              | If the screen does not update, set `egl` or `xrender` |
| `picom_animations`           | `true`             | Show animations when windows open and close   |
| `bash_history_size`          | `100000`           | Number of commands in the history that `Ctrl + R` searches |
| `default_editor`             | `/usr/local/bin/nvim` | Editor for git, crontab and sudoedit. For nano, set `/bin/nano`. |
| `neovim_version`             | `v0.12.6`          | Neovim release from GitHub                    |
| `lazyvim_extras`             | Python, TypeScript, Go, ... | LazyVim extras (languages)           |
| `screenshot_path`            | `~/Pictures/Screenshots` | Folder for the screenshots              |
| `copyq_max_items`            | `500`              | Number of items in the clipboard history      |
| `uv_version`                 | `0.9.28`           | uv release from GitHub                        |
| `pyenv_version`              | `v2.6.3`           | pyenv version (git tag)                       |
| `pyenv_python_versions`      | `3.12.11`          | Python versions that pyenv builds             |
| `nvm_version`                | `v0.40.3`          | nvm version (git tag)                         |
| `nvm_node_version`           | `v24.2.0`          | Default Node.js version                       |

### Keyboard shortcuts

`Super` is the Windows key.

| Keys                     | Action                                    |
|--------------------------|-------------------------------------------|
| `Super + Enter`          | Open a terminal. New windows open on the right. |
| `Super + P`              | Open the application launcher             |
| `Alt + Ctrl + Enter`     | Run a command                             |
| `Alt + Ctrl + P`         | Run a bash alias                          |
| `Super + Shift + T`      | Show the application grid                 |
| `Super + Shift + G`      | Go to a window                            |
| `Super + Shift + B`      | Move a window to this workspace           |
| `Super + 1` to `7`       | Go to a workspace                         |
| `Super + Shift + 1` to `7` | Move the window to a workspace          |
| `Super + W`, `E`, `R`    | Go to monitor 1, 2 or 3                   |
| `Super + Tab`            | Use the next layout (tall, monocle, floats, grid, tabs) |
| `Super + Ctrl + Tab`     | Reset the layout of the workspace         |
| `Super + Space`          | Set full screen on or off                 |
| `Super + F`              | Set the floating layout on or off         |
| `Super + Shift + C`      | Close the window                          |
| `Alt + Shift + 1`        | Change the keyboard layout (US or Arabic) |
| `Super + Shift + L`      | Lock the screen                           |
| `Super + Shift + X`      | Open the power menu. The power button also opens it. |
| `Super + Shift + S`      | Apply the saved monitor layout again      |
| `Super + Shift + R`      | Restart xmonad                            |
| `Print`                  | Make a screenshot of an area. Select the area and draw on it. Then push `Enter` to copy or `Ctrl + S` to save. |
| `Super + Print`          | Do the same after 3 seconds, for example to include a menu |
| `Shift + Print`          | Make a screenshot of this monitor         |
| `Ctrl + Print`           | Make a screenshot of all the monitors     |
| `Super + V`              | Show the clipboard history                |

You can also click these items on the bar:

- The workspaces and the layout name.
- The Debian logo: it opens the launcher.
- Wi-Fi and Bluetooth: click to open the menu. Right-click to set it on or off.
- The volume: click to open the mixer. Scroll to change the volume.
  Middle-click to mute.
- The keyboard layout: click to change the layout.

Wi-Fi and Bluetooth show on the bar only when the device has the hardware. In
this case, the playbook installs NetworkManager (`network-manager`) and BlueZ
(`bluez`).

- The Wi-Fi menu shows the networks. For a new network, it asks for the
  password.
- The Bluetooth menu connects paired devices. It also scans for new devices to
  pair.
- When a keyboard must have a passkey to pair, a notification shows the
  passkey. Type the passkey on that keyboard.

### Terminal (st)

The playbook builds st 0.9.3 with the patches in `roles/base/files/st/patches`.
The top of each patch file tells what the patch does and where it comes from.

- scrollback: st keeps 2000 lines of history.
- alpha: the background is transparent (`st_alpha`). The text stays opaque.
- boxdraw: st draws lines and boxes (Neovim borders, file trees) with no gaps.
- ligatures: the font shows `=>`, `!=` and `->` as one symbol.
- undercurl: Neovim shows spelling errors and code errors with curly lines.
- newterm and copyurl: these patches add the keys in the table below.
- anysize: st fills its tile and puts the text in the center.

| Keys                             | Action                                     |
|----------------------------------|--------------------------------------------|
| Mouse wheel                      | Scroll the history. In `less`, `man` and Neovim, the wheel scrolls the program. |
| `Shift + PageUp` / `PageDown`    | Scroll the history one page. When you push a key, st goes back to the bottom. |
| `Ctrl + Shift + Enter`           | Open a new terminal in the same directory  |
| `Alt + L`                        | Copy the previous URL on the screen. Push again to copy the URL before it. |
| `Alt + Shift + L`                | Copy the next URL                          |
| `Ctrl + Shift + C` / `V`         | Copy to the clipboard / paste from the clipboard |
| `Ctrl + Shift + PageUp` / `PageDown` | Make the text larger / smaller. `Ctrl + Shift + Home` sets the normal size. |

### Shell

The playbook keeps all the bash history in `~/.bash_history`, with the date of
each command. Bash writes each command to the file when the command ends. If
you close a terminal, its commands stay in the file.

| Keys          | Action                                                   |
|---------------|----------------------------------------------------------|
| `Ctrl + R`    | Search the history of all the terminals (fzf)            |
| `Ctrl + T`    | Insert a file path                                       |
| `Alt + C`     | Go to a directory                                        |
| `** + Tab`    | Complete paths, SSH hosts and processes (`ssh **`, `kill **`) |

The prompt has two lines. The command line has the full width of the window:

```
~/p/w/i/devops/ansible-playbook   main   .venv
❯
```

- The parent directories show only their first letter. The window title of st
  shows the full path (`user@host: directory`). The bar and the tabs layout
  show this title.
- In a git repository, the prompt shows the git branch.
- When a Python virtual environment is active, the prompt shows its name.
- `❯` is red when the last command failed.
- The prompt shows `user@host` only over SSH.

`~/.config/bash/bashrc` also loads the developer tools. The playbook installs
nvm, pyenv (with pyenv-virtualenv) and uv. You install Pulumi and Rust
manually. Each tool loads only when it is installed.

The playbook adds one block to the end of `~/.bashrc`. This block reads
`~/.config/bash/bashrc`. The playbook removes the fzf lines and the developer
tool lines from `~/.bashrc`, because the block loads them. Your other lines in
`~/.bashrc` do not change.

### Neovim (LazyVim)

Debian 13 has Neovim 0.10. LazyVim must have Neovim 0.11.2 or newer and the
tree-sitter CLI 0.26 or newer. The playbook installs these versions from GitHub
to `/opt`. To start Neovim, run `nvim`, or open "Neovim" in the launcher.

- The color scheme follows `theme_name`.
- When you yank text, Neovim copies it to the system clipboard. The text is
  then also in the clipboard history.
- `:Lazy` shows the plugins. `:Mason` shows the language servers.
  `:LazyExtras` shows the extras. To keep an extra, add it to `lazyvim_extras`.
- To change the configuration, edit the files in `roles/base/templates/nvim/`.
  Then run the playbook.

### Screenshots

flameshot saves the screenshots in `~/Pictures/Screenshots`. The file name has
the date and the time. When you copy a screenshot to the clipboard, flameshot
also saves the file. The `screenshot` and `xscreenshot` aliases also use
flameshot.

### Clipboard

CopyQ keeps the clipboard history: text and images. The items stay after the
program that copied them closes.

1. Push `Super + V`.
2. Type to search.
3. Push `Enter` to paste the item.

To close the window, push `Esc`.

### Developer tools

The playbook installs these tools in your home directory:

- uv in `~/.local/bin`.
- pyenv in `~/.pyenv`, with the pyenv-virtualenv plugin and the Python
  versions in `pyenv_python_versions`.
- nvm in `~/.nvm`, with the Node.js version in `nvm_node_version` as the
  default.

To update a tool, change its version in `roles/base/defaults/main.yml`. Then
run the playbook. If you run `uv self update` or `pyenv update`, the playbook
sets these versions again. Programs that start from the desktop also use the
default Node.js version, for example Mason in Neovim from the launcher.

# TODO:
- Automate the installation and configuration of the NVIDIA driver
- Back up the Firefox tabs
- Configure OBS
- Enable the NVIDIA drivers for Steam
