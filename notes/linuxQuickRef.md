### Linux Quick Command Reference

#### Check systemd Unit Load Times
`systemd-analyze blame`

#### List all Active systemd Services
`systemctl list-units | grep service`
NOTE: Shows loaded units by default, use `--all`to see inactive
#### List all Active systemd Sockets
`systemctl list-units | grep socket`

#### Change User Shell
`usermod --shell /bin/newshell username`

#### Launch and detach process from terminal
`$command & disown`

#### Check for DNF upgrade
`dnf check-upgrade`

#### Refresh DNF Package Cache
`sudo dnf upgrade --refresh`
NOTE: Use if something is insisting there's an upgrade available, but `dnf upgrade` says there's nothing to do.

#### Set `suspend` to lower power-state
To check and temporarily set the suspend mode:
``` bash
# to check current power state
cat /sys/power/mem_sleep
# To make temporary change, as root
echo deep > /sys/power/mem_sleep
```

To make the change permanent, append `mem_sleep_default=deep` to the end of the `GRUB_CMDLINE_LINUX` line in the `/etc/default/grub` file. Save the file and run:
`sudo grub2-mkconfig -o /boot/efi/EFI/fedora/grub.cfg`