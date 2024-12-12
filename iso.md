## Download ISO and check for integrity

From the Arch Linux [downloads page](https://archlinux.org/download/) download:

- `archlinux-YYYY.MM.DD-x86_64.iso` (about a GB)
- `archlinux-YYYY.MM.DD-x86_64.iso.sig`;

You can download from a mirror over HTTP or use a torrent client (in this case use the magnet link).

```bash
# Ensure signature is from an Arch developer
# Find .iso.sig file on Arch Downloads > Checksums
# Ensure .iso.sig and .iso are in the same directory
gpg --keyserver-options auto-key-retrieve --verify archlinux-YYYY.MM.DD-x86_64.iso.sig

# Ensure ISO's checksum matches the checksum listed on Arch Downloads
# Use e.g. SHA256 checksum from Arch Downloads > Checksums
shasum -a 256 archlinux-version-x86_64.iso
```

### Confirm PGP signature (verbose)

Scroll down on the Arch Downloads page a bit until you reach the section `Checksums`.
Download the `PGP Signature` (which should be a `.iso.sig` file)

```bash
# You need GnuPG
which gpg && gpg --version

# Ensure Arch ISO (.iso)  and signature (.iso.sig) are in the same directory
ls

# Extract PGP signature
gpg --keyserver-options auto-key-retrieve --verify archlinux-version-x86_64.iso.sig
```

You'll get an output something like:

```bash
gpg: assuming signed data in 'archlinux-2021.11.01-x86_64.iso'
gpg: Signature made Mon Nov  1 03:56:17 2021 EDT
gpg:                using RSA key 4AA4767BBC9C4B1D18AE28B77F2D434B9741E8AC
gpg:                issuer "pierre@archlinux.de"
gpg: Good signature from "Pierre Schmitz <pierre@archlinux.de>" [unknown]
gpg: WARNING: The key's User ID is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 4AA4 767B BC9C 4B1D 18AE  28B7 7F2D 434B 9741 E8AC
```

Then check the key fingerprint matches the key of an [Arch linux developer](https://archlinux.org/people/developers/).
Reference: https://wiki.archlinux.org/title/Installation_guide#Verify_signature

### Confirm SHA Checksum (verbose)

Scroll down on the Arch Downloads to the `Checksums` section and record the `SHA1` string, which should be 40-character alphanumeric string,
e.g. `d81bff7f9b05653b048529d741edcce99bc97819` for the November 2021 ISO.

```bash
# Extract SHA256 checksum from ISO
shasum -a 256 archlinux-YYYY.MM.DD-x86_64.iso

# Example output
d81bff7f9b05653b048529d741edcce99bc97819  archlinux-2021.11.01-x86_64.iso
```

## Flashing ISO

Reference: https://wiki.archlinux.org/title/USB_flash_installation_medium#Using_the_ISO_as_is_(BIOS_and_UEFI)

```bash
# Insert and identify USB drive (do not mount the drive!)
lsblk

# Flash the ISO with any of these options
# Reference:  https://wiki.archlinux.org/title/USB_flash_installation_medium#Using_basic_command_line_utilities

cat path/to/archlinux-YYYY.MM.DD-x86_64.iso > /dev/sdx
dd bs=4M if=archlinux-YYYY.MM.DD-x86_64.iso of=/dev/sdx conv=fsync oflag=direct status=progress
cp path/to/archlinux-YYYY.MM.DD-x86_64.iso /dev/sdx
tee < path/to/archlinux-YYYY.MM.DD-x86_64.iso > /dev/sdx
pv path/to/archlinux-YYYY.MM.DD-x86_64.iso > /dev/sdx

# Physically remove USB drive
```
