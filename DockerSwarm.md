# Docker Swarm

## Contents

* [Docker Service](#docker-service)
* [Docker Stack](#docker-stack)

## Docker Service

Initialize a swarm.

```
$ docker swarm init
```

Join a swarm cluster.

```
$ docker swarm join HOST:PORT
$ docker swarm join-token worker|manager
$ docker swarm join --token SWMTKN-1-xxxxxxxxxxxxxxxxxxxxxxxxxxxxx 192.168.65.2:2377
```

Start service.

```
$ docker service create --name webapp --replicas=6 nginx
$ docker service ls
```

Leave a swarm cluster.

```
$ docker swarm leave
```

Lock a new swarm cluster.

```
$ docker swarm init --autolock
```

Lock the existing swarm cluster.

```
$ docker swarm update --autolock=true
$ docker swarm unlock-key    # the cluster need to be unlocked for this to work.
```

Unlock a swarm cluster.

```
$ docker swarm unlock
Please enter unlock key: 
```

Inspecting a service.

```
$ docker inspect webapp
```

## Docker Stack

Stack contains multiple services running in multiple containers in different nodes and configured using a docker stack file.

Deploying a stack.

```
$ docker stack deploy --compose-file docker-stack.yml mystack
```

List the stack.

```
$ docker stack ls
```

List the tasks in the stack.

```
$ docker stack ps mystack
```

List the services in a stack.

```
$ docker stack services mystack
```
