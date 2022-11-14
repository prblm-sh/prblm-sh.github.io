Notes on rustLang Dev environment setup

# Editor Setup
## Install
VSCode via [official Microsoft Repos](https://code.visualstudio.com/docScodes/setup/linux#_rhel-fedora-and-centos-based-distributions)
```bash
# Import msft VSCode GPG Signing Key
sudo rpm --import https://packages.microsoft.com/keys/microsoft.asc

# Add VsCode Repo to /etc/yum.repos.d/
sudo sh -c 'echo -e "[code]\nname=Visual Studio Code\nbaseurl=https://packages.microsoft.com/yumrepos/vscode\nenabled=1\ngpgcheck=1\ngpgkey=https://packages.microsoft.com/keys/microsoft.asc" > /etc/yum.repos.d/vscode.repo'

# Tell DNF to check for updates, should check new repo
# use '--refresh' to force skipping cache
dnf check-update --refresh
# Install VsCode
sudo dnf install code
```

## Config
Need to install the 'remote' extensions, and setup a dedicated headless VM for development. Using fedora 35 server for consistency's sake. Or because I'm a masochist who's going to spend just as much time updating the VM as I do actually writing code.

Probably going to want to setup offloading compilation from the VM to the host in order to avoid excess comp times from limited VM resources. 

### VS Code Extensions
- Remote - SSh
	- Connect to 'remote' VM

- rust-analyser
	- Specifically *Not* the 'Rust Programming Lang' extension, reportedly abandonware.
	- Code Completion
	- Reference and Docs made available in editor
	- Syntax Highlighting
