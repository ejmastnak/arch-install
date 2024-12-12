The plan is to install extra packages before installing the boot loader.
E.g.

```bash
pacman -S efibootmgr mtools dosfstools networkmanager reflector rsync base-devel linux-headers git xdg-utils xdg-user-dirs inetutils dnsutils alsa-utils pulseaudio pavucontrol bash-completion zsh zsh-completions acpi acpi_call acpid tlp util-linux man-db man-pages
```

Notes: 

- `networkmanager` for network management
- `mtools` and `dosfstools` are file system tools
- `reflector` creates mirror list and `rsync` is used by `reflector`
- `base-devel` and `linux-headers` are development packages
- `git` is Git
- `xdg-utils` `xdg-user-dirs` are standard packages for working with common directories and configuring which programs are used to open which file types
- `inetutils` and `dnsutils` are utitilies for IP address stuff
- `alsa-utils` and `pulseaudio`, and `pavucontrol` are all audio packages
  Note: `pipewire-pulse` and `pulseaudio` conflict with each other, use one or the other
- `bash-completion` is bash completion
- `acpi`, `acpi_call`, and `acpid` are for power management
- `tlp` is for battery and power optimization. See [https://wiki.archlinux.org/title/TLP](https://wiki.archlinux.org/title/TLP)
- `util-linux` contains various useful system utilities
- `man-db` and `man-pages` install the standard manual pages.

Import any required PGP keys if prompted.

### Graphics drives

If you have integrated Intel graphics: don't install anything extra, the built-in `mesa` driver works fine.

If you have a discrete graphics card: choose one based on graphics card manufacturer and model:

```bash
pacman -S xf86-video-intel   # discrete Intel graphics
pacman -S xf86-video-amdgpu  # newer AMD graphics
pacman -S xf86-video-ati     # older AMD graphics
pacman -S nvidia nvidia-utils nvidia-settings  # NVIDIA graphics
```

### Enable `systemd` services

Enable some of the services associated with the just-installed packages

```bash
systemctl enable tlp
systemctl enable acpid
systemctl enable reflector.timer
systemctl enable fstrim.timer
```
