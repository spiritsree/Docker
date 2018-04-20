# Docker

## Contents

* [What is Docker](#what-is-docker)
* [Running a Container](#running-a-container)
* [Cleanup](#cleanup)

## What is Docker

Docker is a self-contained sealed unit of software with all the requirements to run the software.

Container

* Code
* Config
* Processes
* Networking
* Dependencies

```
|---------|      docker run    |------------------|
|  Image  | -----------------> | Running Container| -------|
|_________|                    |__________________|        | 
                                                           | exit
|-------------|  docker commit |-------------------|       |
|  New Image  | <------------- | Stopped Container |<------|
|_____________|                |___________________|   
```

## Running a Container

Run a container in interactive terminal (--ti) mode.

```
$ docker run -ti ubuntu bash
root@a23acba9d7ea:/# exit
$
```

Run a command and remove (--rm) the container.

```
$ docker run --rm ubuntu echo Hello World
Hello World
$
```

Run a container in detached mode.

```
$ docker run -d -ti ubuntu bash
d6fb4e90102b57063e54a3283bd50ba268c7cb83fb8d0b1b7538d6f90b719bfc
$
$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
d6fb4e90102b        ubuntu              "bash"              3 seconds ago       Up 4 seconds                            condescending_hugle
```

Attach to a running container.

```
$ docker attach d6fb4e90102b
root@d6fb4e90102b:/# ^P ^Q   # to detach from a container.
$
```

## Cleanup

```
$ docker system prune
WARNING! This will remove:
        - all stopped containers
        - all networks not used by at least one container
        - all dangling images
        - all build cache
Are you sure you want to continue? [y/N] y
Deleted Containers:
dd5588e2719751085ca1b2ad0cdab20916780249f644745e03fb6a6f5f974d59
86de719ca65973154f0d898d7ad97aa882fcb73dba502bb67b09591c918a3e03

Deleted Networks:
helloservice_webnet

Total reclaimed space: 65B
$
```
