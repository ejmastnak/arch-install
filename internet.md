## Connecting to the internet

You need an internet connection to install Arch Linux (because you need to download packages not included on your installation medium from remote repositories).

Reference: [the ArchWiki's page on network configuration](https://wiki.archlinux.org/title/Network_configuration)

```bash
# Identify wireless and wired network interfaces
# Wired/Ethernet network interfaces are prefixed with `en`
# Wireless interfaces are prefixed with `wl`
# Make note of available interface names (e.g. `eno1` or `wlan0`)
# The `-c` flag produces colored output for better readability
ip -c link
ip -c address
```

No wireless interfaces? Try `rfkill unblock wifi` and then `ip link` again.
Enable or disable interfaces with e.g. `ip link set wlan0 up|down`

### Ethernet

Should be plug and play.
Assuming an Ethernet network interface is listed by `ip link`, just plug an Ethernet cable into your computer, via a USB-adapter if needed.

```bash
# Verify a working Internet connection (exit ping with CTRL-C)
ping archlinux.org
```

### Wireless

```bash
# Find the name of your wireless interface (e.g. wlan42)
ip link

# Should open a `[iwd] #` prompt
iwctl

# List names of available networks
station wlan42 get-networks

# Enter password if prompted
station wlan42 connect bar_network

exit

# Verify a working Internet connection (exit ping with CTRL-C)
ping archlinux.org
```
