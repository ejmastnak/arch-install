Goal: remove rEFInd from macOS from back when I didn't know what I was doing and used rEFInd as a dual boot solution.

See https://www.rodsbooks.com/refind/installing.html#uninstalling

For non-ancient version of rEFInd installed with default options,
rEFInd files will be in `EFI/refind` or `EFI/BOOT` on the system's ESP.

Mount the ESP, best to use the `mountesp` script that ships with rEFInd---the script identifies ESP and outputs ESP and mount point to console.

Remove the `refind` directory.
