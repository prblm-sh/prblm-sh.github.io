Methods to upgrade a dumb shell to a fully interactive one

## Python
```bash
python -c 'import pty; pty.spawn("/bin/bash")'
```