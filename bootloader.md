### Install and configure bootloader

This step installs the `systemd` boot loader as [recommended in the ArchWiki](https://wiki.archlinux.org/title/Mac#Using_the_native_Apple_bootloader_with_systemd-boot_(Recommended))

See also https://wiki.archlinux.org/title/Systemd-boot#Configuration

In the `chroot`-ed environment with the `[root@archiso /]#` prompt, enter

```bash
bootctl --path=/boot install
```

Above command should create a `/boot` directory with a bunch of other files and directories inside.

```bash
cd /boot/loader/entries
vim arch.conf  # create a new boot loader entry file
```

In the `arch.conf` file, enter the following

```bash
title   Arch Linux
linux   /vmlinuz-linux
initrd  /intel-ucode.img
initrd  /initramfs-linux.img
options root=/dev/sdaROOT rw
```

Save file and exit text editor.


```bash
cd /boot/loader

# Let's tweak the boot loader config file
vim loader.conf
```

Expect three lines, the first two commented out with `timeout` and `console-mode` values.
The third line should be `default` with a long alphanumeric string. Change the file to something like

```bash
# Time in seconds between the boot loader menu appearing and booting the first listed operating system
timeout 10

# Something about console registering the resolution of your display
# You could also set `console-mode max` to use maximum display resolution.
console-mode keep

# Boot loader entry to load
default arch.conf
```

Save file and exit text editor.
