Reference: https://wiki.archlinux.org/title/Installation_guide

## Installation

### Prepare ISO

See dedicated notes.

### Boot

Prequisite: you have an Arch ISO flashed to a USB.

- Power off computer
- Insert USB, power on, and hold the manufacturer-specific key to bring up your computer's boot manager (see notes). Boot from USB drive.

### Preliminaries

```bash
# Verify UEFI boot mode
ls /sys/firmware/efi/efivars
```

### Connect to internet

See dedicated notes.

### Update system clock

```bash
timedatectl set-ntp true
```

### Partitions

See dedicated notes.

## Install kernel and preliminary packages

```bash
# Or amd-ucode for machines with an AMD processor
pacstrap /mnt base linux linux-firmware intel-ucode vim
```

This step involves downloading and installing around a gigabyte of files and can take a while, depending on your internet connection.

## Create file sytem table

```bash
# Generate file system table and store it at `/mnt/etc/fstab`
genfstab -U /mnt >> /mnt/etc/fstab
```

Theory: A file system table (among other things) stores the partition mount points specified in the mounting step.
You can generate an FS table using either partition UUID (use `genfstab` with the `-U` option) or a partition label (use `genfstab` with the `-L` option).
We use UUIDs; they are always unique and reliable. 

## Main installation

```bash
# Make `/mnt` the apparent root directory for the rest of the installation
# This should bring up a new command prompt, '[root@archiso /]#'
arch-chroot /mnt
cat /etc/fstab  # optional: if you want to check the fstab file
```

### Swap file

```bash
# For an e.g. 1024M swap file (adjust as needed based on RAM)
dd if=/dev/zero of=/swapfile bs=1M count=1024 status=progress
chmod 600 /swapfile
mkswap /swapfile
swapon /swapfile
```

Then put `/swapfile` into `/etc/fstab` as follows

```bash
# In /etc/fstab, on a blank new line with a line between, add:
/swapfile none swap defaults 0 0
```

### Localization

```bash
# Set time zone (adjust as needed)
# See `timedatectl list-timezones` for a full list
ln -sf /usr/share/zoneinfo/US/Eastern /etc/localtime
```

```bash
# Synchronize hardware and system clocks
hwclock --systohc
```

```bash
# Configure the locale file
vim /etc/locale.gen
# All locales are initially commented out.
# Find desired locale, e.g. `en_US.UTF-8 UTF-8`, and UNCOMMENT THE `UTF8` VERSION
```

Save and exit text editor. Then...

```bash
# Generate locales after editing `/etc/locale.gen`
locale-gen

# Set LANG variable
echo LANG=en_US.UTF-8 >> /etc/locale.conf
```

### Optional: keyboard layout

You need to do this only if you want to use a different keyboard layout than the default `en_US`.
You do this by setting the `KEYMAP` variable in the `/etc/vconsole.conf` file.

```bash
# For example...
echo KEYMAP=de_CH-latin1 >> /etc/vconsole.conf
```

### Network hostname configuration

```bash
# Set a hostname
echo ej-t480 >> /etc/hostname
```

Create/edit `/etc/hosts` and on a new line enter

```bash
# Separate entries with tabs
# Replace hostname with your hostname
127.0.0.1    localhost
::1          localhost
127.0.1.1    ej-t480.localdomain  ej-t480
```

### Password for root user

Enter `passwd` and follow prompts: Enter new password and confirm the password. Should be straightforward. This is the administrator password.
  
### Install extra packages

See dedicated notes.

### Create a user

```bash
# E.g. useradd -mG wheel ejmastnak
useradd -mG wheel ejmastnak
passwd username
```

```bash
# Give the just-created user `sudo` privileges
echo "ejmastnak ALL=(ALL) ALL" >> /etc/sudoers.d/{username}

# Edit sudoers file using vim as EDITOR and uncomment line with "%wheel ALL=(ALL) ALL"
EDITOR=vim visudo
```

### Install and configure bootloader

See dedicated notes.

### Exit installer and reboot

```bash
# Exit the chrooted environment (command line prompt should change)
exit

# Unmount the `/mnt` partition.
# Recently this consistently gives me a "target is busy" error. I just poweroff in this case.
umount -R /mnt

# Shut down the computer.
poweroff
```

Wait for the computer to completely power off, then remove the USB drive. 

Turn the computer back on and reboot into Arch Linux.
If you reboot into Arch Linux successfully then you reach a command prompt with something like

```bash
ej-t480 login:
```

Enter username and password.
This should then take you to a prompt with e.g. `[ejmastnak@ej-t480 ~]$ `

Proceed to `post-install.md`
