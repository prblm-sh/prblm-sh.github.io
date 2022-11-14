### Systemd - Linux `init` and System Management

#### Removing Systemd Units
To prevent a service from running automatically and also stop the unit:
`systemctl disable --now UNITNAME`
To remove the unit/service from the system, use `dnf` to uninstall/remove and check the following the directories for orphaned files related to the service.
``` bash
/etc/systemd/system/
/usr/local/etc/systemd/system/
home/user/.config/systemd/user/
/usr/lib/systemd/
/usr/local/lib/systemd/
/etc/init.d/
```
Alternatively, use the `find` command to find all instances of filenames of the service.

#systemd 