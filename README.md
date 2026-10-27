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
| Terminal      | st       | `st_config.h.j2`                             |
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

### Keyboard shortcuts

`Super` is the Windows key.

| Keys                     | Action                                    |
|--------------------------|-------------------------------------------|
| `Super + Enter`          | Open a terminal                           |
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

You can also click the bar: workspaces, the layout name, the Debian logo (launcher),
Wi-Fi and Bluetooth (click: menu, right click: on/off), the volume (click: mixer,
scroll: volume, middle click: mute), the keyboard layout and the power icon.

Wi-Fi and Bluetooth are only in the bar when the device has the hardware. The
playbook then installs NetworkManager (`network-manager`) and BlueZ (`bluez`).
The Wi-Fi menu lists the networks and asks for the password of a new network.
The Bluetooth menu connects paired devices, and scans for new devices to pair.
When a keyboard needs a passkey to pair, a notification shows the passkey to type on it.

### Monitors

Use `arandr` to set the monitor layout, then save it as
`~/.screenlayout/screen.sh`. xmonad runs this file when it starts.

# TODO:
- automate Nvidia dirver installation & configuration
- firefox tabs backup
- OBS config
- enable steam Nvidia drivers
