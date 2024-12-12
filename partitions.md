## Partitioning

We will use the GPT partitioning scheme and UEFI booting.
An Arch Linux installation requires, at a minimum,

1. an EFI system partition, and
1. a root partition.

In this guide I will create:

1. an EFI system partition
1. a root partition
1. a home partition

## Identify existing partitions

For orientation, to get an overview of your partitions, enter one of...
```bash
lsblk        # list block devices (disks)
fdisk -l     # more verbose output than lsblk
```

Based on size, identify the disk and partition identifiers for both the USB you are booting from and the computer's built-in hard drive.

## Creating partitions for Linux

Assumptions:

- The built-in hard drive's identifier `sda` (`nvme0n1` is also common for NVME drives)
- *You are not dual booting, and will wipe the old OS*

Process:

- `cgdisk /dev/sda`
- Delete all existing partitions, which should result in `Free Space`.
- Use `[New]` to create partitions in the `Free Space`—see instructions below.
- As names you can use e.g. `efi`, `root`, and `home`, but this is only for human-readable purposes.
- `[Write]`, confirm with `yes`, then `[Quit]`. 
- `lsblk` should show proper partitions after finishing.

### EFI partition

- `[New]` to create a new partition.
- Use default first sector
- For last sector enter `+300M` for a 300 MB EFI partition.
- Use hex code `ef00`, which should correspond to the partition type `EFI system partition`

### Root partition

- `[New]` to create a new partition.
- Default first sector
- Last sector: e.g. `+30G` for a 30 GB root partition.
- Enter `8300` for partition type/code; the `8300` code corresponds to the `Linux filesystem` partition type.

### Home partition

- `[New]` to create a new partition.
- Default first sector.
- Default last sector, which uses up entire remaining disk space.
- Enter `8300` for partition type/code; the `8300` code corresponds to the `Linux filesystem` partition type.

When complete, select `[Write]` to write partition changes to disk, confirm with `yes`, then `[Quit]`. Confirm that `lsblk` shows proper partitions after exiting `cgdisk`.

## Format partitions

```bash
# EFI
mkfs.vfat /dev/sdaEFI

# Root
mkfs.ext4 /dev/sdaROOT

# Home
mkfs.ext4 /dev/sdaHOME
```

## Mount partitions

(Use `lsblk` to check mount points after a `mount` command.)

```bash
# Root
mount /dev/sdaROOT /mnt

# EFI, assuming use of systemd-boot
mkdir /mnt/boot  # create /mnt/boot directory
mount /dev/sdaEFI /mnt/boot

# Home
mkdir /mnt/home
mount /dev/sdaHOME /mnt/home
```
