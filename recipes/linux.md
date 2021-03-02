# Linux
## Knowledge base
### Set less to follow output
Press shift+F.
### A window is minimized and can't be maximized (fluxbox)
Often happens when restoring an IntelliJ session for example. The window state gets restored incorrectly and iconifies some windows. Can be fixed by right clicking on the window in the task bar and clicking 'Iconify'.
### Restore an accidentally deleted file (ext filesystems)
Rule 1: immediately (re)mount the filesystem read-only once you notice your stupid mistake, to avoid overwriting data
```bash
# Remount read-only
sudo mount /mnt/disk -r remount,ro
# Try to recover the file
sudo extundelete --restore-file path/relative/to/root.txt /dev/disk
# If the above was successful, the file is now at RECOVERED_FILES/path/relative/to/root.txt
# You can now remount and restore the file
sudo mount /mnt/disk -r remount,rw
cp RECOVERED_FILES/path/relative/to/root.txt /mnt/disk/path/relative/to/root.txt
```
### Fix paste after it got messed up by bracketed paste
The bracketed paste setting can get messed up in some terminals, for example after exiting vim in terminator. If weird characters get pasted together with the thing you're trying to paste, run the following to repair your terminal:
```bash
printf "\e[?2004l"
```


## Configuration tweaks
### Desktop
#### Fix theming of Qt5 applications running outside KDE
To fix Qt5 theming in for example fluxbox
Install `qt5ct` (you may have to `add-apt-repository ppa:hda-me/qt5ct` first) and add below to `.profile` to make sure applications started without a shell pick it up too:
```bash
if [ `type -P qt5ct` ]; then
  export QT_QPA_PLATFORMTHEME="qt5ct"
fi
```
See https://launchpad.net/~hda-me/+archive/ubuntu/qt5ct
### SSH
#### Enable multiplexing / connection reuse
In `~/.ssh/config`
```
host *
  ciphers aes256-ctr,aes256-cbc,aes192-ctr,aes192-cbc,aes128-ctr,aes128-cbc
  ControlMaster auto
  ControlPath ~/.ssh/sockets/%r@%h-%p
  ControlPersist 300
```

#### Attach to a remotely running DBUS session
```bash
. ~/.dbus/session-bus/* && export DBUS_SESSION_BUS_ADDRESS DBUS_SESSION_BUS_PID
```

### Network
#### Restrict VPN traffic and DNS to whatever is supposed to be routed over it
When activating VPN, the routing tables and your DNS configuration may get messed up. This causes private traffic and/or DNS to pass over VPN.

First determine the link ID for the tunnel and your network interface:
```bash
TUN_DEVICE="tun0" # replace with actual value (or script parameter)
ETH_DEVICE="enp2s0f1" # replace with actual value (or script parameter)

TUN_LINK_ID="$(ip link show dev "$TUN_DEVICE")"
TUN_LINK_ID="${TUN_LINK_ID%%:*}"
ETH_LINK_ID="$(ip link show dev "$ETH_DEVICE")"
ETH_LINK_ID="${ETH_LINK_ID%%:*}"
```

Then, apply the correct DNS settings:
```bash
# DBUS destination for systemd-resolved
DBUS_DEST="org.freedesktop.resolve1"
DBUS_NODE="/org/freedesktop/resolve1"
# in the call below, add "<domain> false" (without quotes) for every domain you want to resolve over the VPN's DNS server
# the number in front must match the number of domains (ia(sb) means: an array of string boolean tuples preceded by an integer equal to the array's size)
sudo busctl call "$DBUS_DEST" "$DBUS_NODE" "${DBUS_DEST}.Manager" "SetLinkDomains" "ia(sb)" "$TUN_LINK_ID" 3 foo.com false bar.net false baz.io false
# using the same approach, reset DNS for the local network and all other domains
# replace lan with your local domain name
sudo busctl call "$DBUS_DEST" "$DBUS_NODE" "${DBUS_DEST}.Manager" "SetLinkDomains" "ia(sb)" "$ETH_LINK_ID" 2 lan false . true
```

If the routing tables are messed up, fix them by replacing the bad default route with specific ones:
```bash
sudo ip route del default dev "$TUN_DEVICE"
# repeat for every route you want to use the tunnel for
sudo ip route add "10.0.0.0/8" dev "$TUN_DEVICE"
```

### System
Note: changes made in `/etc/sysctl.conf` can be applied immediately by running `sudo sysctl -p --system`
#### Reduce swappiness
If the system has lots of RAM and swap is placed on an SSD, it's best not to use it too much. To keep swapping to a minimum, while not completely disabling it, add below to `/etc/sysctl.conf`:
```
vm.swappiness = 1
```
#### Increase the maximum number of inotify watches
Some IDE's, for example IntelliJ, rely on inotify to watch for file changes. For large projects this may create a lot of watches, possibly depleting them. Add below to `/etc/sysctl.conf`:
```
fs.inotify.max_user_watches = 524288
```
See [Inotify Watches Limit - IntelliJ IDEA - Confluence](https://confluence.jetbrains.com/display/IDEADEV/Inotify+Watches+Limit) for more info.
