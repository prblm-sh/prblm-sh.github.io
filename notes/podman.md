### Podman (Pod Manager)

[PodmanBasics](https://github.com/containers/podman/blob/main/docs/tutorials/podman_tutorial.md)

[Podman Cockpit Docs](https://docs.oracle.com/en/operating-systems/oracle-linux/8/tutorial-cockpit-podman/#Managing-Podman-Images)

[Podman Man Page](http://docs.podman.io/en/latest/markdown/podman.1.html)

##### Container Image Registries
Below enabled by default after installing podman via the cockpit web UI.
`registry.fedoraproject.io`
`registry.access.redhat.com`
`docker.io`
`quay.io`
All of the above registries are owned by Redhat/IBM

NOTE: Container **Command**: the command that should be run when the container starts. This value is automatically populated, but you can change the value if you wish. The command must be facilitated within the container image. If the command cannot run within the image, the container fails to run.

##### Pulling New Images
Login to local cockpit web UI, select the `Podman Containers` page. Select `Get new image` and search for, fuck I don't know, something related to the project. There's a lot.

##### Root vs Rootless
Running Fedora34, we're already setup with cgroupsV2, therefore theoretically we're already setup to be able to run containers rootless.

##### Daemonless
Podman does *NOT* run with a daemon by default. Therefor, images can be managed via a *USER* systemd config file in `~/.config/systemd/user`, and files to use with systemd can be generated from Podman via the `generate` command, i.e.
`podman generate systemd --name CONTAINERNAME --files`
`systemctl --user daemon-reload`
`systemctl --user enable --now container-CONTAINERNAME.service`
`systemctl --user --status container-CONTAINERNAME.service`

#podman #container 
