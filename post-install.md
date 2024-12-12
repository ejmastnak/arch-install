## Post-Installation

```bash
# Update clocks as in initial install
sudo timedatectl set-ntp true
sudo hwclock --systohc
```

## Packages

```bash
# Some might already be installed
sudo zsh zsh-completions \
vifm openssh networkmanager pass fzf udisks2 \
xorg xorg-xinit xss-lock xbindkeys i3-wm i3status i3blocks i3lock picom dmenu alacritty feh redshift ttc-iosevka \
zathura zathura-pdf-mupdf texlive
```

## NetworkManager

```bash
# If needed
systemctl enable --now systemd-resolved.service

# Disable currently running network daemons if active
systemctl disable dhcpcd.service
systemctl stop dhcpcd.service
systemctl disable systemd-networkd.service
systemctl stop systemd-networkd.service

systemctl enable --now NetworkManager.service

reboot
```

## Clone dotfiles

```bash
git clone https://github.com/ejmastnak/dotfiles

cd dotfiles && bash ./install
```

Then open Neovim and `PlugInstall`.

And make all scripts in `dotfiles/scripts` executable if necessary.

## ZSH

```bash
# For root
sudo chsh -s /usr/bin/zsh

# For current user
chsh -s /usr/bin/zsh
```

Optionally create `/root/.zshrc` and place `EDITOR=nvim` to use nvim with `visudo`.

## caps2esc

```bash
sudo pacman -S interception-caps2esc
```

Create `/etc/udevmon.yaml` with

```yaml
- JOB: "intercept -g $DEVNODE | caps2esc | uinput -d $DEVNODE"
  DEVICE:
    EVENTS:
      EV_KEY: [KEY_CAPSLOCK, KEY_ESC]
```

Create `/etc/systemd/system/udevmon.service` with

```sh
[Unit]
Description=udevmon
Wants=systemd-udev-settle.service
After=systemd-udev-settle.service

# Use `nice` to start the `udevmon` program with very high priority,
# using `/etc/udevmon.yaml` as the configuration file
[Service]
ExecStart=/usr/bin/nice -n -20 /usr/bin/udevmon -c /etc/udevmon.yaml

[Install]
WantedBy=multi-user.target
```

Start `udevmon`:

```bash
sudo systemctl enable --now udevmon.service
```

## pass and credentials

- Copy `~/.password-store` and `~/.ssh` to new computer
- Import pass GPG private key to new computer

## Graphical environment

See dedicated i3 notes.
