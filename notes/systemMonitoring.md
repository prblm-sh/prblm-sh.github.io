### Linux System Monitoring/Auditing
List of services that have been disabled/removed on local system.

To monitor/audit services;
`systemctl list-units | grep service`
For sockets;
`systemctl list-units | grep socket`
AND/OR
`systemctl list-sockets`
To see the system files associated with a service
`systemctl cat SERVICENAME`

#### Disabled Systemctl Services
`cups.service` -  Printing Services
`lvm2-monitor.service` - lvm2 daemon, not using LVM on current install
`livesys-late.service` - From liveCD install
`livesys.service` - From liveCD install

#### Disabled Sockets
`lvm2-lvmpolld.socket` - lvm2 daemon, not being used

##### References
[linuxQuickRef]({% link notes/linuxQuickRef.md%})