# FirewallD

## File Configs
### Zones
Firewalld is a zone-based firewall. `zones` define the overall trust level for a network connection. Each zone may govern multiple network interfaces, however each interface may only be part of one active zone. 

For example, below shows the output of `firewall-cmd --get-active-zones`:
``` bash
FedoraWorkstation
  interfaces: wlp0s20f3
libvirt
  interfaces: virbr0
```
The `FedoraWorkstation` zone is covering the `wlp0s20f3` (wifi) interface, while the `libvirt` zone is covering the `virbr0` virtual interface.
Here are the default rules and services for the `FedoraWorkstation` zone:
```bash
FedoraWorkstation (active)
  target: default
  icmp-block-inversion: no
  interfaces: wlp0s20f3
  sources: 
  services: dhcpv6-client mdns samba-client ssh
  ports: 1025-65535/udp 1025-65535/tcp
  protocols: 
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
```
And the default rules/services for the `libvirt` zone:
```bash
libvirt (active)
  target: ACCEPT
  icmp-block-inversion: no
  interfaces: virbr0
  sources: 
  services: dhcp dhcpv6 dns ssh tftp
  ports: 
  protocols: icmp ipv6-icmp
  forward: no
  masquerade: no
  forward-ports: 
  source-ports: 
  icmp-blocks: 
  rich rules: 
	rule priority="32767" reject
```

Zones are the highest level of control in firewalld, and allow many configuration options for specific interfaces, connections, or ports and services. 

Each zone can be configured to your needs, as well as creating custom zones.
Predifined/default zones (and other rules/services) are located at `/usr/lib/firewalld/`, while custom/user-defined zones are located at `/etc/firewalld`. The `/etc/firewalld` directory is also where current/runtime options are loaded from. 

### Services
Services allow specifying rules for ports or protocols, and can be used with multiple zones.
The advantage of services over individual ports is multiple ports can be specified in a single service. For example, setting up `samba` (which is conveniently a predefined service in firewalld) requires ports 137/udp, 138/udp, 139/tcp, and 445/tcp. If we wanted to use the `samba` service with multiple zones, we can set the `samba` service on each instead of having to set each port individually.

### IPSet
An `ipset` is used to group multiple IP addresses (both ipv4/6 supported, but must be specifed separately) or MAC addresses for for allow/deny lists

### Helper
Firewalld `helper` modules are the configs for netfiler connection tracking if automatic helper assignment is turned off.
I don't know what this means in english. 

### ICMP Type
`icmptype`config files allow defining rules for ICMP messages/packets. 

### Direct Interface
`direct` config options bypass the userspace `firewalld` runtime and interact directly with the *backend* of the firewall. The specifics of how direct rules behave varies based on whether the backend is IPtables, EBtables, or NFtables.

###### Links
Updated to 1.0 with the release of Fedora 35
[firewalld 1.0 release](https://firewalld.org/2021/06/the-upcoming-1-0-0)

## Command Line
The command line tool is `firewall-cmd`, for more information see [firewall-cmd]({% link notes/firewall-cmd.md%}) or `man firewall-cmd`.
NOTE: By default, any changes made with `firewall-cmd` only affect the current runtime environmen of the firewall and *will **not*** be preserved across reboots/reloads unless the `--permanent` flag is used. 
