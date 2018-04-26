# Docker Orchestration using Swarm

## Contents

* [Docker Service](#docker-service)
* [Docker Stack](#docker-stack)
* [Troubleshooting Docker Swarm](#troubleshooting-docker-swarm)

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

Update the replcias.

```
$ docker service update --replicas=10
```

Global service. Global service crete exactly one job (container) in each nodes. The default is replicated which create replicas of same container in different nodes.

```
$ docker service create --mode global --name global-service nginx
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

Adding a volume to a service.

```
$ docker service update --mount-add type=volume,source=temp-vol,target=/temp-vol mystack_web
$ docker service inspect mystack_web | grep vol
```

Listing local volumes.

```
$ docker volume ls
```

## Docker Stack

Stack contains multiple services running in multiple containers in different nodes and configured using a docker stack file.

Deploying a stack. Updating the yml file and rerunning the command will update the stack.

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

## Troubleshooting Docker Swarm

Service logs will show logs from service.

```
$ docker service logs SERVICE|STACK

e.g:

$ docker service logs mystack_web
$ docker service logs -f mystack_web
```

Docker Swarm node labels give you control over exactly where your services will run.

```
$ docker node update --label-add PRI NODE1
$ docker service create --name redis_service --constraint 'node.labels.type == PRI' redis:3.0.6
```
