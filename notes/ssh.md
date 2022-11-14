# SSH - Secure Shell

## Config File

### Host Aliases
For hosts that are frequently accessed, the connection options can be defined in `~/.ssh/config` for user configuration.
For more info, see `man ssh_config`
An example config:

```
Host cloud
	Hostname longcloudname
	User	 clouduser
	Port	 2222
```
With the above config file, 
`ssh cloud` would be the same as 
`ssh -p 2222 clouduser@longcloudname`