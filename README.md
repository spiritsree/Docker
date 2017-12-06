# Docker

## Table of Contents

* [Installation](#installation)
  * [CentOS](#centos)
  * [Ubuntu](#ubuntu)
  * [Scripted Installation](#centos)
* [Uninstall Docker](#uninstall-docker)
  * [CentOS](#centos)
  * [Ubuntu](#ubuntu)
* [Docker Containers](#docker-containers)
  * [Building the APP](#building-the-app)
  * [Running the app](#running-the-app)
  * [Publish the Image](#publish-the-image)
* [Docker Services](#docker-services)
  * [Define Service](#define-service)
  * [Start Service](#start-service)
  * [Test the Service](#test-the-service)
  * [Scaling the app](#scaling-the-app)
  * [Stopping the app and Swarm](#stopping-the-app-and-swarm)
* [Swarm Clusters](#swarm-clusters)
* [Cheat Sheet](#cheat-sheet)

## Installation

### CentOS

1. Install required packages.

```
$ sudo yum install -y yum-utils device-mapper-persistent-data lvm2
```

2. Configure Repo.

```
$ sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
```

3. Install docker.

```
$ sudo yum install docker-ce
```

4. start docker.

```
$ sudo systemctl start docker
```

5. Test docker.

```
$ sudo docker run hello-world
```

6. Check docker version.

```
$ docker --version
```

### Ubuntu

1. To allow Docker to use the aufs storage drivers.

```
$ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual
```

2. Install packages to allow apt to use a repository over HTTPS.

```
$ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
```

3. Add Docker’s official GPG key.

```
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```

4. Check the fingerprint .

```
$ sudo apt-key fingerprint 0EBFCD88 | grep '9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88'
```

5. Setup repository.

```
$ sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

6. Update the apt package index.

```
$ sudo apt-get update
```

7. Install the latest version of Docker CE.

```
$ sudo apt-get install docker-ce
```

   * On production systems, you should install a specific version of Docker CE instead of always using the latest. This output is truncated. List the available versions.

``` 
$ apt-cache madison docker-ce 
docker-ce | 17.09.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages
```

```
$ sudo apt-get install docker-ce=<VERSION>
```

8. Test docker.

```
$ sudo docker run hello-world
```

9. Check docker version.

```
$ docker --version
```

### Scripted Installation

1. Download the script.

```
curl -fsSL get.docker.com -o get-docker.sh
```

2. Run

```
sudo sh get-docker.sh
```


## Uninstall Docker

### CentOS

1. Remove docker.

```
$ sudo yum remove docker-ce
```

2. To delete all images, containers, and volumes.

```
$ sudo rm -rf /var/lib/docker
```

### Ubuntu

1. Remove docker.

```
$ sudo apt-get purge docker-ce
```

2. To delete all images, containers, and volumes.

```
$ sudo rm -rf /var/lib/docker
```


## Docker Containers

A container image is a lightweight, stand-alone, executable package of a piece of software that includes everything needed to run it: code, runtime, system tools, system libraries, settings.

Docker container is defined using a Dockerfile.

### Building the APP

To build the app, you need to have all the required files in directory.

```
$ cd app
```

The directory with all the required files.

```
$ ls
  Dockerfile		app.py		requirements.txt
```

Required python modules.
 
```
$ cat requirements.txt
  Flask
  Redis
```

The app itself.

 ```python
$ cat app.py 
  from flask import Flask
  from redis import Redis, RedisError
  import os
  import socket
	
  # Connect to Redis
  redis = Redis(host="redis", db=0, socket_connect_timeout=2, socket_timeout=2)
  
  app = Flask(__name__)

  @app.route("/")
  def hello():
    try:
      visits = redis.incr("counter")
    except RedisError:
      visits = "<i>cannot connect to Redis, counter disabled</i>"

    html = "<h3>Hello {name}!</h3>" \
      "<b>Hostname:</b> {hostname}<br/>" \
      "<b>Visits:</b> {visits}"
    return html.format(name=os.getenv("NAME", "world"), hostname=socket.gethostname(), visits=visits)
 
    if __name__ == "__main__":
      app.run(host='0.0.0.0', port=80)
```

Dockerfile which contains the definition for the container.

```
$ cat Dockerfile 
  # Use an official Python runtime as a parent image
  FROM python:2.7-slim
	
  # Set the working directory to /app
  WORKDIR /app
	
  # Copy the current directory contents into the container at /app
  ADD . /app
	
  # Install any needed packages specified in requirements.txt
  RUN pip install --trusted-host pypi.python.org -r requirements.txt
	
  # Make port 80 available to the world outside this container
  EXPOSE 80
	
  # Define environment variable
  ENV NAME World
	
  # Run app.py when the container launches
  CMD ["python", "app.py"]
```

To build the app run the following command.

```
$ docker build -t <appname> .
```

  e.g:

```
docker build -t helloapp .
```

Image will be added to the local machine docker registry.

``` 
$ docker images 
  REPOSITORY                 TAG                 IMAGE ID            CREATED             SIZE
  helloapp                   latest              d9e8193e0ecf        22 hours ago        148MB
```
	
  OR

```
$ docker image ls
```

### Running the app

To run the app run the command. This will start the container using the image from local machine registry.

```
$ docker run -p <HostIP>:<HostPort>:<ContainerPort> <ImageName>
```

```
$ docker run -p 80:80 helloapp
```

Test the app by accessing the url.

```
$ curl http://127.0.0.1
  <h3>Hello World!</h3><b>Hostname:</b> c79723544b3c<br/><b>Visits:</b> <i>cannot connect to Redis, counter disabled</i> 
```

Run the app in detached mode.

```
$ docker run -d -p 80:80 helloapp
```

Use the following command to check the running containers.

```
$ docker container ls
  CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS                    NAMES
  c79723544b3c        helloapp            "python app.py"     3 minutes ago       Up 4 minutes        127.0.0.1:4000->80/tcp   keen_stonebraker 
```


### Publish the Image

1. Log in to the Docker public registry on your local machine.

```
$ docker login
```

2. Tag the image

```
$ docker tag <Image> <Username>/<Repository>:<Tag>
```

  for e.g:

```
$ docker tag helloapp spiritsree/test_app:test
```

```
$ docker images
  REPOSITORY               TAG                 IMAGE ID            CREATED             SIZE
  helloapp                 latest              d9e8193e0ecf        22 hours ago        148MB
  spiritsree/test_app      test                d9e8193e0ecf        3 minutes ago       148MB
```

3. Upload your tagged image to the repository:

```
$ docker push <Username>/<Repository>:<Tag>
```

4. You can run this app from any docker platform.

```
$ docker run -p 80:80 <Username>/<Repository>:<Tag>
```

## Docker Services

A service is a group of containers of the same image:tag. Services make it simple to scale your application.

### Define Service

A `docker-compose.yml` file is a YAML file that defines how Docker containers should behave in production.

```yaml
version: "3"
services:
  web:
    # replace username/repo:tag with your name and image details
    image: <username>/<repo>:<tag>
    deploy:
      replicas: 5
      resources:
        limits:
          cpus: "0.1"
          memory: 50M
      restart_policy:
        condition: on-failure
    ports:
      - "80:80"
    networks:
      - webnet
networks:
  webnet:
```

### Start Service

First you need to make the node as a swarm manager or join the swarm cluster.

```
$ docker swarm init
```

Now start the service.

```
$ docker stack deploy -c docker-compose.yml <appname>
```

e.g:

```
$ docker stack deploy -c docker-compose.yml helloapp
```
### Test the Service

Check the service.

```
$ docker service ls
```

In the above e.g: the service name will be `helloapp_web`.

You can list the container by using the command,

```
$ docker service ps helloapp_web
```

You can also list the tasks using,

```
$ docker container ls -q
```

Test the service using the curl command.

```
curl -4 http://localhost
```

Each time you get different hostname which confirms that the load balancing is working.

### Scaling the app

You can scale the app by changing the `replicas` value in `docker-compose.yml`.

```
$ docker stack deploy -c docker-compose.yml helloapp
```

Docker will do an in-place update, no need to tear the stack down first or kill any containers.

### Stopping the app and Swarm

Take the app down with docker stack rm:

```
$ docker stack rm helloapp
```

Take down the swarm.

```
$ docker swarm leave --force
```


## Cheat Sheet

   ```
	docker build -t <appname> .  		# Create image using this directory's Dockerfile
	docker run -p 80:80 <appname>  		# Run "app" mapping port 80 to 80
	docker run -d -p 80:80 <appname>         # Same thing, but in detached mode
	docker container ls                                # List all running containers
	docker container ls -a             # List all containers, even those not running
	docker container stop <hash>           # Gracefully stop the specified container
	docker container kill <hash>         # Force shutdown of the specified container
	docker container rm <hash>        # Remove specified container from this machine
	docker container rm $(docker container ls -a -q)         # Remove all containers
	docker image ls -a                             # List all images on this machine
	docker image rm <image id>            	# Remove specified image from this machine
	docker image rm $(docker image ls -a -q)   	# Remove all images from this machine
	docker login             # Log in this CLI session using your Docker credentials
	docker tag <image> username/repository:tag  	# Tag <image> for upload to registry
	docker push username/repository:tag            # Upload tagged image to registry
	docker run username/repository:tag                   # Run image from a registry
	docker stack ls                                            # List stacks or apps
	docker stack deploy -c <composefile> <appname>  # Run the specified Compose file
	docker service ls                 # List running services associated with an app
	docker service ps <service>                  # List tasks associated with an app
	docker inspect <task or container>                   # Inspect task or container
	docker container ls -q                                      # List container IDs
	docker stack rm <appname>                             # Tear down an application
	docker swarm leave --force      # Take down a single node swarm from the manager
   ```

