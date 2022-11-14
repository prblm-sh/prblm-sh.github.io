# Virsh
[Virsh Man Pages](https://libvirt.org/manpages/virsh.html)

# Quick Command Ref

## Guest Console
The console service in the guest is not enabled/started by default. on the *VM GUEST,* run the following;
```bash
# On GUEST
systemctl enable --now serial-getty@ttyS0.service
# On HOST
sudo virsh console VM_GUEST
```

## Get Guest VM IP
```bash
sudo virsh domifaddr VM_GUEST_NAME
```

## Start/Stop Guest VM
```bash
# Start VM
sudo virsh start VM_GUEST_NAME

# Shutdown VM Gracefully
sudo virsh shutdown VM_GUEST_NAME

## Force Guest VM Shutdown
sudo virsh destroy VM_GUEST_NAME
```

## Remove Guest VM
```bash
## Remove Guest VM/Domain
sudo virsh undefine VM_GUEST_NAME

## Remove Guest and all associated storage
sudo virsh undefine VM_GUEST_NAME --remove-all-storage
```

## Snapshots
##### List Snapshots of a Guest VM
```bash
sudo virsh snapshot-list GUEST_VM_NAME
```

##### Revert to a Snapshot
```bash
sudo virsh snapshot-revert GUEST_VM_NAME \
SNAPSHOT_NAME  --running
```

##### Create a Snapshot
```bash
sudo virsh snapshot-create-as --domain GUEST_VM_NAME \
--name "nameOfSnapshot" --description "description of VM"
```
N.B> `virsh snapshot-create` sans `-as` is an available command, however using the `-as` option allows a name and description from the command line

##### Delete a Snapshot
```bash
sudo virsh snapshot-delete --domain \
GUEST_VM_NAME --snapshotname SNAPSHOT_NAME
```

## Update Virt Network
```bash

```