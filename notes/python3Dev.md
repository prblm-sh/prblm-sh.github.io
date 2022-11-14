---
aliases: [python, python3]
---
### Python3 Development
[python3QuickRef]({% link notes/python3QuickRef.md%})

##### Virtual Machine (Server)

##### Container (Podman)

##### Virtual Environment (Poetry?)

##### PipX
[pipX](https://pypi.org/project/pipx/)
*Does NOT* need `sudo` permission to run. Ever.
Installs python scripts that are designed to be called standalone from the CLI (mostly).
Creates an isolated virtEnv for *each* install in `~/.local/pipx/venvs` and adds them to `~/.local/bin/`
Pipx can also be used to run scripts/apps directly, by installing in a temp directory by calling `pipx run packagename` and will automatically pass arguments to the script as they are normally by the CLI. 

##### PDM
[introToPDM](https://frostming.com/2021/01-22/introducing-pdm/)
[PEP582](https://www.python.org/dev/peps/pep-0582/)
*NOTE:* `pdm --pep582 >> ~/.bash_profile` our similar must be ran to enable the PEP582 standard globally in order for this to function with your system python installation. 

Recommended install is via `pipx install pdm`
Recommended to add `.pdm.toml` and `__pypackages__` to `.gitignore`.

Based on PEP 582 from 2018, proposes similar directory structure to the node_js ecosystem, shown below. 

```text
pythonProject.d
├── __pypackages__
│   └── 3.8
│       └── lib
└── my_script.py
```
Following this, instead of running installing .py packages into a virtualenv, packages/libraries are listed in the `_pypackages__` directory, and then can be imported in the scripts inside the project directory. The !SYSTEM! installed python is called to run the script, i.e. `python3 /path/to/pythonProject.d/my_script.py`.
Modules installed following PEP582, i.e. `simpleHTTPServer`, get installed *In the directory python is invoked FROM*, and are a *local user* install by default.
Under this standard, if we run `python3 -m pip install anyPackage` from our home directory, the python binary will attempt to install the package into the home directory. *ANY* python commands that are related to the project *MUST* be run *from that project directory.*
*WHEN RUNNING A SCRIPT FROM IT'S DIRECTORY*, if it has a `__pypackages__` directory in the *same path* as the script, packages will be loaded from *the scripts location*, not where the python binary was invoked. 
