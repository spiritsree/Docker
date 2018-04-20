# Docker

## Contents

* [What is Docker](#what-is-docker)
* [Running a Container](#running-a-container)
* [Networking](#networking)
* [Storage](#storage)
* [Stopping a Container](#stopping-a-container)
* [Images](#images)
* [Dockerfile](#dockerfile)
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

### Networking

Start container with a listening port.

```
$ docker run --rm -ti -d -p 9999:9999 -p 9997:9997 --name echo-server ubuntu:14.04 nc -lp 9999 
16d14a5209db42408f6bdf6620b4f108554fd73650980cb221c12762f63c70ca
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED                  STATUS              PORTS                                            NAMES
16d14a5209db        ubuntu:14.04        "nc -lp 9999"       Less than a second ago   Up 3 seconds        0.0.0.0:9997->9997/tcp, 0.0.0.0:9999->9999/tcp   echo-server
```

Check the avalable ports.

```
$ docker port echo-server
9999/tcp -> 0.0.0.0:9999
9997/tcp -> 0.0.0.0:9997
```

Linking docker containers. Once the link breaks the connection won't be established.

```
$ docker run --rm -ti --name server ubuntu:14.04  bash 
root@b5b20c20b3f8:/# nc -lp 9999 
hello
```
```
$ docker run --rm -ti --link server --name client ubuntu:14.04 bash
root@573cf83a68a6:/# nc server 9999
hello
```

Dynamic linking. The conenction will be established even after connection break.

```
$ docker network create my_network
ddbd824c8de2ebd4abf2cc2cdc3e703b34a25789b70409618f64b6cfaaa96d52
$ docker network list
NETWORK ID          NAME                DRIVER              SCOPE
ddbd824c8de2        my_network          bridge              local
```
```
$ docker run --rm -ti --name server --net=example ubuntu:14.04  bash 
root@25eaaf4a0a85:/# nc -lp 9999
hello
```
```
$ docker run --rm -ti --link server --name client --net=example  ubuntu:14.04 bash
root@94c4a5251ad7:/# nc server 9999
hello

```

## Storage

Permanent volume. The data will be available even after destroying containers. `-v local_dir:shared_dir`.

```
$ docker run --rm -ti -v /Python:/shared_Volume  ubuntu:14.04  bash 
```

Ephemeral volume. The data will be destroyed once all the containers inheriting the volume is destroyed.

```
$ docker run --rm -ti -v /shared_Volume  ubuntu:14.04  bash 
$ docker run --rm -ti -v /shared_Volume --volumes-from 188947a78295  ubuntu:14.04  bash
```

## Stopping a Container

Use exit if connected to container or use kill a detached running container.

```
$ docker kill c3dc43d18170
c3dc43d18170
```
Check the containers

```
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS                        PORTS               NAMES
c3dc43d18170        ubuntu              "bash"              5 seconds ago       Exited (137) 3 seconds ago                        hungry_noyce
ffc8d7f40dbf        ubuntu              "bash"              36 seconds ago      Exited (0) 47 seconds ago                         upbeat_johnson
46d69c4db84c        ubuntu              "bash ls"           14 minutes ago      Exited (126) 14 minutes ago                       zen_haibt
```

Check the logs of a stopped container.

```
$ docker logs 46d69c4db84c
/bin/ls: /bin/ls: cannot execute binary file
```

## Images

List all local images.

```
$ docker images -a
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
ubuntu              latest              c9d990395902        7 days ago          113MB
```

Creating an image from container.

```
$ docker commit 5d91817ac797 my-ubuntu
sha256:224dc2cbf7d538daa9d8da5d1e9bd48ea1abca2c2e78d6754245e6089d620884
$ docker images 
REPOSITORY          TAG                 IMAGE ID            CREATED                  SIZE
my-ubuntu           latest              224dc2cbf7d5        Less than a second ago   223MB
ubuntu              16.04               c9d990395902        7 days ago               113MB
ubuntu              14.04               3b853789146f        7 days ago               223MB
```

Creating an image with custom tag.

```
$ docker commit 5d91817ac797 my-ubuntu:prod
sha256:9a340be12d4676e40ff94ae31cfa7c4daddfa1dbb15643a311c06e9fabfde15f
roadbodysat-lm:Python gsree$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED                  SIZE
my-ubuntu           prod                9a340be12d46        Less than a second ago   223MB
my-ubuntu           latest              224dc2cbf7d5        About a minute ago       223MB
ubuntu              16.04               c9d990395902        7 days ago               113MB
ubuntu              14.04               3b853789146f        7 days ago               223MB
```

Pulling an image to local repo.

```
$ docker pull debian
Using default tag: latest
latest: Pulling from library/debian
c73ab1c6897b: Pull complete 
Digest: sha256:c908a4fcb2b2a1953bd40ebc12d9a4116868d72540efc27502ee6c2395b8a1e9
Status: Downloaded newer image for debian:latest
```

Pushing a new image to registry.

```
$ docker push registry-host:5000/myadmin/my-ubuntu
```

Tag an image.

```
$ docker tag my-ubuntu:prod my-ubuntu:v2.0
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
my-ubuntu           v2.0                9a340be12d46        8 minutes ago       223MB
```

Searching for an image.

```
$ docker search ubuntu
NAME                                                      DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
ubuntu                                                    Ubuntu is a Debian-based Linux operating sys…   7535                [OK]                
dorowu/ubuntu-desktop-lxde-vnc                            Ubuntu with openssh-server and NoVNC            179                                     [OK]
~~~~~~~~~ truncate ~~~~~~~
```

## Dockerfile

Dockerfile is used to build a custom image.

```
$ cat Dockerfile 
FROM ubuntu:16.04

RUN echo "This is a custom build"
RUN echo "This is Second echo"

CMD echo "Hello World!"
```

Build the image from Dockerfile.

```
$ docker build -t ubuntu_nc .
Sending build context to Docker daemon  3.284MB
Step 1/4 : FROM ubuntu:16.04
 ---> c9d990395902
Step 2/4 : RUN echo "This is a custom build"
 ---> Running in f618feb446ac
This is a custom build
Removing intermediate container f618feb446ac
 ---> 5e1c92b4bac5
Step 3/4 : RUN echo "This is Second echo"
 ---> Running in b2c3c8df0fb0
This is Second echo
Removing intermediate container b2c3c8df0fb0
 ---> 5fa385dc0f52
Step 4/4 : CMD echo "Hello World!"
 ---> Running in f85aacbf1ad8
Removing intermediate container f85aacbf1ad8
 ---> 999652aca5b2
Successfully built 999652aca5b2
Successfully tagged ubuntu_nc:latest
```

## Cleanup

System cleanup of unused stuff.

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

Remove stopped containers.

```
$ docker rm c3dc43d18170 ffc8d7f40dbf 46d69c4db84c
c3dc43d18170
ffc8d7f40dbf
46d69c4db84c
$ docker ps -a
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
```

Remove unused images.

```
$ docker rmi c9d990395902
Untagged: ubuntu:latest
Untagged: ubuntu@sha256:9ee3b83bcaa383e5e3b657f042f4034c92cdd50c03f73166c145c9ceaea9ba7c
Deleted: sha256:c9d990395902a9e219684af847a63d793ccf72cf2aadeece1b576566c5662400
$ docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
$ 
```


