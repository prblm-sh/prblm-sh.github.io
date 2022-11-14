# SSH Tunneling Magic
## Local Tunnel
To send local traffic over an SSH connection:
```bash
ssh user@remotehost -L 127.0.0.1:9999:127.0.0.1:9090
```
The above command redirects all (tcp?) traffic on the current *localhost* port 9999 to the *remotehost* port 9090.
If we have a `cockpit` instance setup on the *remotehost,* this would allow us access the *remote* instance of `cockpit` by navigating too `https://127.0.0.1:9999` in our *localhost* browser. 

## SOCKS5 Proxy
To send ALL traffic through the ssh tunnel:
```bash
ssh user@remotehost -D $UNUSEDPORTNUMBER
```
If using a browser, browser proxy settings must be set to listen on 127.0.0.1 at the same port number used when initiating connection, i.e. `ssh user@vulnbox -D 9050`, proxy would be set to 127.0.0.1 port 9050.