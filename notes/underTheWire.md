# UnderTheWire - Powershell WarGames

[Under The Wire](https://underthewire.tech/wargames)

## Quick Ref
### PS Commands
```Powershell
Get-Help
Get-Command
Get-Member
```

## Century
### Connect//Level 0
To Connect to first level:
```bash
ssh century1@century.underthewire.tech
```
Password: `century1`
Note: `~/.ssh/config` set to allow
```bash
ssh century1@century
```

### Levels
#### Century 1
The password for Century2 is the **build version** of the instance of PowerShell installed on this system.  
  
**NOTE:**  
– The format is as follows: ****.*.*****.******  
– Include all periods  
– Be sure to look for build version and NOT PowerShell version  
  
  
**IMPORTANT:**  
Once you feel you have completed the Century1 challenge, start a new connection to the server, and log in with the username of Century2 and this password will be the answer from Century1. If successful, close out the Century1 connection and begin to solve the Century2 challenge. This concept is repeated over and over until you reach the end of the game.