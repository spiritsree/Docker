# Docker

## Installation

### CentOS

1. Install required packages.

` sudo yum install -y yum-utils device-mapper-persistent-data lvm2 `

2. Configure Repo.

` sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo `

3. Install docker.

` sudo yum install docker-ce `

4. start docker.

` sudo systemctl start docker `

5. Test docker.

` sudo docker run hello-world `

### Ubuntu

1. To allow Docker to use the aufs storage drivers.

` $ sudo apt-get install linux-image-extra-$(uname -r) linux-image-extra-virtual `

2. Install packages to allow apt to use a repository over HTTPS.

` $ sudo apt-get install apt-transport-https ca-certificates curl software-properties-common `

3. Add Docker’s official GPG key.

` curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add - `

4. Check the fingerprint .

` sudo apt-key fingerprint 0EBFCD88 | grep '9DC8 5822 9FC7 DD38 854A E2D8 8D81 803C 0EBF CD88' `

5. Setup repository.

` sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" `

6. Update the apt package index.

` $ sudo apt-get update `

7. Install the latest version of Docker CE.

` $ sudo apt-get install docker-ce `

    * On production systems, you should install a specific version of Docker CE instead of always using the latest. This output is truncated. List the available versions.

    ``` $ apt-cache madison docker-ce 
        docker-ce | 17.09.0~ce-0~ubuntu | https://download.docker.com/linux/ubuntu xenial/stable amd64 Packages ```

    ` $ sudo apt-get install docker-ce=<VERSION> `

8. Test docker.

` $ sudo docker run hello-world `

### Scripted Installation

1. Download the script.

` curl -fsSL get.docker.com -o get-docker.sh `

2. Run

` sudo sh get-docker.sh `


## Uninstall Docker

### CentOS

1. Remove docker.

` sudo yum remove docker-ce `

2. To delete all images, containers, and volumes.

` sudo rm -rf /var/lib/docker `

### Ubuntu

1. Remove docker.

` $ sudo apt-get purge docker-ce `

2. To delete all images, containers, and volumes.

` $ sudo rm -rf /var/lib/docker `


