NOTE: As of `docker` version 1.13 (Release January '07), `tini` is included/built into docker via the `--init` flag, which I believe carries over into the `podman` compatibility efforts. 

`tini` is a very, very simple init system, `tini` only does one thing, and that is spawn a single child-process at the start of the container. This process becomes `PID-1`, has built *default signal handling*, and reaps zombie process that get left over in the container.

`tini` has an advantage over over using `bash` or another shell for the container `CMD` entrypoint due to in e.g. `bash`, which may not give you a correct exit code for your program (as of 2015 writing, fuck), and if your bash process exits for any reason, your container dies due to no longer having any running process to spawn new ones.

#tini #container