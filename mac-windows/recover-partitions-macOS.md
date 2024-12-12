## Contents

First, the problem: I partitioned the Macbook's hard drive `/dev/sda` to make dedicated partitions for Linux.
I now want to delete the Linux partitions and have the entire hard drive available for macOS.

#### My initial dual-boot setup
Dual boot with `systemd-boot`.
The `systemd-boot` directory is mounted at `/boot` on Arch Linux, and the `systemd-boot` binary lives at `/boot/EFI/BOOT/BOOTX64.EFI`.

My `diskutil list` output on macOS is
```
/dev/disk0 (internal, physical):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      GUID_partition_scheme                        *500.3 GB   disk0
   1:                        EFI EFI                     209.7 MB   disk0s1
   2:                 Apple_APFS Container disk1         199.9 GB   disk0s2
   3:                 Linux Swap                         4.3   GB   disk0s3
   4:           Linux Filesystem                         32.2  GB   disk0s4
   5:           Linux Filesystem                         263.5 GB   disk0s5
```
Okay the sizes don't really matter but you get the idea. Recognize swap, root and home partitions. The problems is that I would want `Apple_APFS Container disk1` to take up the entire available disk space.

The command `diskutil list` also showed the container scheme
```
/dev/disk1 (synthesized):
   #:                       TYPE NAME                    SIZE       IDENTIFIER
   0:      APFS Container Scheme -                      +199.9 GB   disk1
                                 Physical Store disk0s2
   1:                APFS Volume Macintosh HD - Data     43.0  GB   disk1s1
   2:                APFS Volume Preboot                 82.4  MB   disk1s2
   3:                APFS Volume Recovery                529.0 MB   disk1s3
   4:                APFS Volume VM                      1.1   GB   disk1s4
   5:                APFS Volume Macintosh HD            11.1  GB   disk1s5
```

### Recovering 

#### Removing `systemd-boot`
Boot into Arch Linux and, as root, run
```sh
bootctl status  # for orientation
bootctl remove  # remove systemd-boot files
cd /boot        # change into boot directory
rm -r loader    # manually remove /boot/loader
```
Reference: [`man bootctl`](https://man.archlinux.org/man/bootctl.1), which is short and concise.

- Note that you won't be able to boot back into Arch after `bootctl remove`.

- For my case consider also `bootctl --esp-path=/boot/ remove` to specify the location of the EFI system partition.

- From `man bootctl`, the `bootctl remove command`

  > Removes all installed versions of `systemd-boot` from the EFI system partition and the firmware's boot loader list.

- You need to manually delete `/boot/loader`; it is not removed by `bootctl remove`

Just for orientation, after this after powering on with `Alt`, the macOS boot manager only showed an option for `Macintosh HD`, while previously there were two options: `Macintosh HD` and `EFI Boot`.
So `bootctl remove` worked correctly.
Meanwhile powering the computer on without `Alt` pressed just booted straight into macOS.

#### Deleting Arch partitions
- Create a bootable USB with an Arch ISO
- Boot into the live USB (shut down Mac, power on, press `Alt` immediately after powering on, choose the option `EFI Boot`)
- Enter `lsblk` (or `fdisk -l`) and identify Arch partitions. Identify your root block device, probably `/dev/sda`.
- `cgdisk /dev/sda` (or use `gdisk` or whatever else you like) and delete the Arch partitions.
  What was previously partitions should show up as `Free Space` in `cgdisk`'s interface.
- Power off, i.e. `poweroff` in the command line

#### Resize macOS container
- Power on. I just booted into regular macOS and not Recovery mode.
  
- Open `Terminal`
  First `diskutil list` and identify the Container Reference device for the computer's APFS Container.
  In my case this is `/dev/disk1`, but will in general be different on different systems. See the theory section below.

- After identifying your CR device, in my case `disk1`, enter
  ```sh
  diskutil apfs resizeContainer disk1 0
  ```
  The operation was very fast for me, maybe 30 seconds.
  After this Disk Utility showed `Macintosh HD` as having the full 500 GB hard drive space.
  And that was that, wow. Easy.

- Explanation: The `0` resizes the container to maximum available capacity.
  From `man diskutil` entry on `APFS`
  > You can specify a size of `0` to grow the targeted APFS Physical Store such that all remaining space is filled to the next partition or the end of the partition map.

  For my puposes the call signature is
  ```sh
  diskutil apfs resizeContainer <containerReferenceDevice> <size>
  ```
  My relevant containerReferenceDevice is `disk1` (see theory section below). Note that I don't use `/dev/disk1`, see the examples at the bottom of `man diskutil`

  Reference: `man diskutil`'s section on the `resizeContainer` verb for the `apfs` command.
  If you open the man page in `less` you can just search for the first few words, which are "Resize an existing APFS Container by specifying"



### Theory

#### View EFI partition and its contents on macOS
- Boot into macOS
- Enter `diskutil list`. Identify the name, e.g. `disk0s1`, of your EFI partition
  
  You should also be able to identity the other Linux partitions, e.g. swap, root, home, etc...

- Enter (say) `diskutil mount disk0s1` from a command line on macOS.

- You should then be able to access the EFI partition either at `cd /Volumes/EFI/` from the terminal. Alternatively, an `EFI` disk should appear in the Finder left sidebar under `Locations` after `diskutil mount disk0s1`.
  
  You should then be able to identity files related to Linux and `systemd-boot`, e.g. the `loader` directory, `mvlinuz-linux`, etc...

#### My Macbook 10,2 EFI directory
```
/Volumes/EFI/
├── BOOTLOG
└── EFI
    └── APPLE
        ├── CACHES
        │   └── CAFEBEEF
        ├── EXTENSIONS
        │   └── Firmware.scap
        └── FIRMWARE
            └── MBP102.scap

6 directories, 3 files
```
Interesting, there is no `/EFI/BOOT`. Is this because Apple supposedly does not use the EFI system partition for booting? See [https://wiki.archlinux.org/title/Mac#Using_the_native_Apple_boot_loader_with_GRUB](https://wiki.archlinux.org/title/Mac#Using_the_native_Apple_boot_loader_with_GRUB).


#### Apple containers, partitions, and volumes
Reference: `man diskutil`; search for the `APFS` command. If you open the man page in `less`, search for the words "Apple APFS defines these types of objects"

Summarizing:
- APFS Container: a child of one or more Physical Stores and a parent of zero or more APFS Volumes.

  A Container is identified by it APFS Container Reference disk, which is a synthesized disk exported by APFS for identification purposes only.

  The output of `diskutil list` will show if disk is `(internal, physical)` or `(synthesized)`. In my case the APFS Container Reference disk is `/dev/disk1`

- Associated with each APFS Container is one or more APFS Physical Store disks.
  
  The Physical Store associated with a given Container is shown in the output of `diskutil list`. In my case the Physical Store associated with my container scheme is `disk0s2`

  The Physical Store is closer than a Container to something tangible like a physical hard drive partition, and in my case it is so and `disk0s2` is a physical partition on my hard drive, but things can be more subtle, e.g. with Fusion drives or `APPLERAID` systems.

- APFS Volume: each APFS Container can have in princple zero or more volumes, but in practice will have a few volumes.
  Volumes are "virtual" and exist only in the context of a Container---see the explanation below.

Useful big picture: [https://stackoverflow.com/a/62738358](https://stackoverflow.com/a/62738358).

  Summarizing: The APFS container is the object that occpies physical space, i.e. bytes, on a drive.
  APFS volumes are virtual labels used to allocate space on a container.
  Within an APFS container, the volumes coexist and compete for the container's free space.
  APFS volumes exist within a container but not outside of it.

### Future readings
- [https://apple.stackexchange.com/questions/154964/resizing-or-expanding-a-corestorage-volume](https://apple.stackexchange.com/questions/154964/resizing-or-expanding-a-corestorage-volume)

- [https://itectec.com/askdifferent/resizing-macintosh-hd-partition-to-use-free-space/](https://itectec.com/askdifferent/resizing-macintosh-hd-partition-to-use-free-space/)
