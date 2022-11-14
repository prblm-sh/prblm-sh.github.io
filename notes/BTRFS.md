# BTRFS - Better File System
This is gonna be too deep a rabbit hole to get into right now, I'll fill this out when my heads less heavy.

## Btrfs create new subvolumes
New btrfs subvolumes can not be created directly from directories that already exist.
In order to convert an existing regular directory into a subvolume;
- a subvolume with a temporary name must first be created
- Files from the *directory* that you want to be a *subvolume* must be moved/`mv` into the subvolume that was just created

#### Gotchas
Per [btrfs mailing list](https://www.spinics.net/lists/linux-btrfs/msg33258.html)

Potential Gotcha's on Subvolume Creation:
	One possible gotcha is the ownership of the subvolume at creation time.
	If you do this as root then you need to chown after you're done; or you need to be logged in as the user who will own the Documents directory in each of the four cases.
	If you're using SELinux another gotcha is with copying files, which causes them to inherit the security context of the directory copied into, whereas mv preserves existing context. 
	You can run `restorecon -Rv /home/user` once you're done to make sure the subvolume and its contents have the right labeling.
	
### Example
To create a new subvolume at `/home/user/Documents` example:
```bash
# Create new btrfs subvolume with temp name
btrfs subvolume create /home/user/tmp_Documents

# move all files from directory that's being turned into a subvolume into the subvolume that was just created
mv /home/user/Documents/* /home/user/tmp_Documents/

# Verify that the directory is empty
ls /home/user/documents/
# There should be *no output*

# Remove empty directory
rmdir /home/user/documents/

# Rename the subvolume to the previous name of the directory
mv /home/user/tmp_Documents /home/user/Documents
```


## Replacing Hard Drives
BTRFS has a built-in command to replace failing/old disks, in addition the the `add/remove` commands.
To replace the drive;
``` bash
# List devices in file system
sudo btrfs filesystem show

# Mount the NEW SSD
sudo mount /dev/nvme*** /mnt

# Start the BTRFS replace operation
sudo btrfs replace start 2 /dev/nvme** /mnt

# To check the status of the replace operation
sudo btrfs replace status /mnt
```
If the new device is smaller than the one being replaced, it should be resized with `btrfs fi resize` *before* running the replace command.

If the new drive is larger, it should be resized *after* the replace command has completed.

##### Tags
#empty 