These are extra steps specific to installing Arch on a computer that was previously running Windows (and completely overwriting Windows).

## Installation on Windows

- Disable SecureBoot and Fast Startup in Windows.

  To disable Fast Startup: Control Panel > Power Options > Choose what the power buttons do. If needed click "Change settings that are currently unavailable", then unselect Fast Startup.

  For SecureBoot: [https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/disabling-secure-boot?view=windows-11](https://docs.microsoft.com/en-us/windows-hardware/manufacture/desktop/disabling-secure-boot?view=windows-11)

- Check your disks: Windows key > `Disk Management` (just for orientation)

- Troubleshooting `F12` not bringing up boot menu:
  Ensure the Fast Startup thing didn't turn back on.
  Perform a hard shutdown (hold `Shift` while clicking `Shut Down` in Windows)
  By the way `shutdown /s /t 0` in a Windows command prompt performs a full shut down

- Also if `/sys/firmware/` doesn't contain `efi`, disable the Security chip in BIOS.

