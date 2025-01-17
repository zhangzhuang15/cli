---
title: "exec"
description: "The exec command description and usage"
keywords: "command, container, run, execute"
---

# exec

```markdown
Usage:  docker exec [OPTIONS] CONTAINER COMMAND [ARG...]

Execute a command in a running container

Aliases:
  docker container exec, docker exec

Options:
  -d, --detach         Detached mode: run command in the background
      --detach-keys    Override the key sequence for detaching a container
  -e, --env=[]         Set environment variables
      --env-file       Read in a file of environment variables
      --help           Print usage
  -i, --interactive    Keep STDIN open even if not attached
      --privileged     Give extended privileges to the command
  -t, --tty            Allocate a pseudo-TTY
  -u, --user           Username or UID (format: <name|uid>[:<group|gid>])
  -w, --workdir        Working directory inside the container
```

## Description

The `docker exec` command runs a new command in a running container.

The command started using `docker exec` only runs while the container's primary
process (`PID 1`) is running, and it is not restarted if the container is
restarted.

COMMAND will run in the default directory of the container. If the
underlying image has a custom directory specified with the WORKDIR directive
in its Dockerfile, this will be used instead.

COMMAND should be an executable, a chained or a quoted command
will not work. Example: `docker exec -ti my_container "echo a && echo b"` will
not work, but `docker exec -ti my_container sh -c "echo a && echo b"` will.

## Examples

### Run `docker exec` on a running container

First, start a container.

```console
$ docker run --name ubuntu_bash --rm -i -t ubuntu bash
```

This will create a container named `ubuntu_bash` and start a Bash session.

Next, execute a command on the container.

```console
$ docker exec -d ubuntu_bash touch /tmp/execWorks
```

This will create a new file `/tmp/execWorks` inside the running container
`ubuntu_bash`, in the background.

Next, execute an interactive `bash` shell on the container.

```console
$ docker exec -it ubuntu_bash bash
```

This will create a new Bash session in the container `ubuntu_bash`.

Next, set environment variables in the current bash session.

```console
$ docker exec -it -e VAR_A=1 -e VAR_B=2 ubuntu_bash bash
```

This will create a new Bash session in the container `ubuntu_bash` with environment
variables `$VAR_A` and `$VAR_B` set to "1" and "2" respectively. Note that these
environment variables will only be valid on the current Bash session.

By default `docker exec` command runs in the same working directory set when container was created.

```console
$ docker exec -it ubuntu_bash pwd
/
```

You can select working directory for the command to execute into

```console
$ docker exec -it -w /root ubuntu_bash pwd
/root
```


### Try to run `docker exec` on a paused container

If the container is paused, then the `docker exec` command will fail with an error:

```console
$ docker pause test

test

$ docker ps

CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                   PORTS               NAMES
1ae3b36715d2        ubuntu:latest       "bash"              17 seconds ago      Up 16 seconds (Paused)                       test

$ docker exec test ls

FATA[0000] Error response from daemon: Container test is paused, unpause the container before exec

$ echo $?
1
```
